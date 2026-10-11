---
title: "Serving Large Private Files from Laravel"
date: "2026-10-11"
tags: [ laravel,php,security,web ]
image: img/posts/serving-large-private-files-from-laravel.jpg
---

# Serving Large Private Files from Laravel

(image by [pexels](https://www.pexels.com/))

## Introduction

Let's suppose you need to show a set of large files to a small group of users. Say a few hundred photographs, 6000 x 4000 JPEGs of about 7 MB each, straight from the camera, and **only the members of a team may open them.** Nobody else, logged in or not.

The reason you want to serve the originals might be scientific: the users need to zoom in and inspect details, so downscaling the images is not always a solution. The same applies to scanned documents, medical images, or any file where the detail is the point.

If you have served files from Laravel before, you know the first thing that comes to mind: `php artisan storage:link`, drop the files under `storage/app/public`, and write `<img src="/storage/...">`. It takes five minutes and it works. It is also the **wrong tool** for this case! I hit exactly this scenario in an application I work on, and this post walks through what I built instead and why.

The post is for anyone who serves files that are not public: uploads, datasets, scans, contracts, paid downloads. The code is written on Laravel 13 (PHP 8.3+), but the approach is decoupled from the framework and the version. The principles apply to any web application that needs to check who is asking before handing out a file.

## The naive scenario, and why it fails

The public disk exists for files that anyone may fetch: logos, avatars, marketing assets. The symlink turns `storage/app/public` into `public/storage`, and from then on the web server hands the files out directly. Laravel is not involved in those requests at all. That is the point of the feature, and it is exactly the problem:

- **Anyone who has the URL gets the file.** The session, the roles, and the policies never run.
- **The URLs are guessable**. Files named `11.JPG`, `12.JPG`, `13.JPG` can be fetched with a loop.
- **The symlink must exist on every server.** Forget it on one deployment target and every file is a 404.
- Moving the files later, to another directory or to S3, **changes every URL** the frontend ever rendered.

Signed URLs are the usual answer for the first two points, and they are a good one when the files live on S3. For a directory on the application server, I wanted something simpler: one endpoint that checks who is asking and then streams the file.

## Keep the files where they are, point a disk at them

The files live in a directory outside the Laravel tree. In my case it is a separate git repository, cloned next to the application, because the data has its own history and its own owners. Nothing is copied into `storage/`. The application only needs to know where the directory is, so the path is a config value with an environment override:

```php
// config/files.php
return [
    'root' => base_path(env('FILES_PATH', 'files')),
];
```

A filesystem disk is rooted at that path. It is a local disk with no `url` key, which means Laravel cannot build a public URL for it even by accident:

```php
// config/filesystems.php
'files' => [
    'driver' => 'local',
    'root' => config()->string('files.root'),
    'throw' => false,
    'report' => false,
],
```

The database stores two things per file: the directory of the collection it belongs to (`Expedition_2024`) on the collection row, and the file name (`11.JPG`) on the file row. Together they make the path relative to the disk. The absolute path never leaves the server.

The tests get their own root: `phpunit.xml` sets `FILES_PATH` to a directory with three small committed fixtures, so the suite never touches the real data.

## One authorized endpoint

The route takes the collection the user is browsing and the file they want:

```php
Route::get(
    'collections/{collection}/files/{file}',
    [CollectionFileController::class, 'show'],
)->name('collections.file');
```

The frontend receives this URL inside the page payload and puts it in an `<img src>`. The browser sends the session cookie with the request, as with any other same-origin URL, so nothing special happens on the client.

Authorization lives in a form request. It asks two questions: may this user see files of this collection, and does this file belong to this collection? The second check matters. Without it, a member of one collection could request any file id in the database and the policy check alone would pass:

```php
class ShowCollectionFileRequest extends FormRequest
{
    public function authorize(): bool
    {
        $collection = Collection::query()->findOrFail($this->route('collection'));

        if (! Gate::allows('viewFile', $collection)) {
            return false;
        }

        $file = CollectionFile::query()->findOrFail($this->route('file'));

        return $file->collection_id === $collection->id;
    }
}
```

The policy behind the gate is plain: admins, and users who are members of the collection. The controller then builds the path and streams the file:

```php
class CollectionFileController extends Controller
{
    private const string CACHE_CONTROL = 'private, max-age=86400';

    public function show(ShowCollectionFileRequest $request, int $collection, int $file): StreamedResponse
    {
        $fileRow = CollectionFile::query()->findOrFail($file);
        $collectionRow = Collection::query()->findOrFail($collection);

        $directory = $collectionRow->directory;
        $fileName = $fileRow->file_name;

        abort_unless($this->isSafeSegment($directory), Response::HTTP_NOT_FOUND);
        abort_unless($this->isSafeSegment($fileName), Response::HTTP_NOT_FOUND);

        $relativePath = $directory . '/' . $fileName;

        abort_unless(Storage::disk('files')->exists($relativePath), Response::HTTP_NOT_FOUND);

        return Storage::disk('files')->response($relativePath, null, [
            'Cache-Control' => self::CACHE_CONTROL,
        ]);
    }

    /** A single path segment: non-empty, no separators, no parent references. */
    private function isSafeSegment(mixed $segment): bool
    {
        return is_string($segment)
            && $segment !== ''
            && ! str_contains($segment, '/')
            && ! str_contains($segment, '\\')
            && ! str_contains($segment, '..');
    }
}
```

Two details are deliberate.
First, both path segments come from the database, not from the request, and both are still checked. A row can be edited by hand, an import can have a bug, and `../../.env` in a file name must not become a readable path. Each segment has to be a single directory or file name: no slashes, no backslashes, no `..`. Second, every failure is a 404. A 403 for "wrong collection" and a 404 for "no such file" would tell a curious user which ids exist. One answer for everything leaks nothing.

`Storage::response()` returns a `StreamedResponse`. PHP reads the file in chunks and writes them to the output, so a 7 MB file does not get loaded into memory as a string.

## Cache it privately

The `Cache-Control` header is the one line that makes the approach practical. `private` tells the browser it may keep the file and tells every shared cache on the way that it may not. `max-age=86400` means that for a day, opening the same file again costs no request at all. A user who moves back and forth between images gets each file once.

What is still missing is an `ETag` or a `Last-Modified` header. After the day is over, the browser downloads the file again instead of asking whether it changed. The fix is a few lines in the same controller; I left it out here to keep the example short.

## The tests that pin it down

The feature test for the endpoint has eight cases, and I think the list is worth more than the code, because it is the contract:

- a guest is redirected to the login page;
- a user who is not a member of the collection gets a 403;
- a member gets a 200 with `image/jpeg` and the private cache header;
- an admin gets a 200 without being a member;
- a file that belongs to a different collection gets a 403;
- a file name containing `..` gets a 404;
- a collection with no directory gets a 404;
- a directory containing `..` gets a 404.

The fixtures are three small JPEGs committed to the repository. The real files are never needed to run the suite, which is the only way the suite stays runnable on a fresh clone and on CI.

## Is streaming through PHP fast enough?

Honest answer: for the application I had in mind, yes, and I measured before deciding. One user opens one file at a time. The first load costs one PHP process for about 7 MB; every later load of the same file is served from the browser cache. And since the users zoom into details, generating smaller copies would not have replaced the original anyway; it would only have added a second set of files to keep in sync.

If your numbers are different, if hundreds of users open large files at once, keep the design and move the bytes out of PHP. nginx has `X-Accel-Redirect` and Apache has `X-Sendfile`: the controller still runs the authorization and then returns an empty response with a header that names the file; the web server streams it. The nginx side is one internal location:

```nginx
location /protected-files/ {
    internal;
    alias /var/www/files/;
}
```

And the controller returns the header instead of the stream:

```php
return response('', 200, [
    'X-Accel-Redirect' => '/protected-files/' . $relativePath,
    'Content-Type' => $fileRow->mime,
    'Cache-Control' => self::CACHE_CONTROL,
]);
```

The policy, the path checks and the tests stay the same. Only the last line changes, which is the reason to keep the authorization in Laravel in the first place.

## Three things that need attention

**EXIF orientation.** Browsers rotate a JPEG according to its EXIF Orientation tag, but `getimagesize()` returns the raw sensor dimensions. If you store width and height at import time, for example so the page can reserve the right aspect ratio before the image loads, a phone photo with Orientation 6 ends up stored as landscape and displayed as portrait. Read the tag and swap the two values for orientations 5 to 8:

```php
set_error_handler(static fn (): bool => true);

try {
    $exif = exif_read_data($absolutePath);
} finally {
    restore_error_handler();
}

$rotated = $exif !== false && in_array($exif['Orientation'] ?? 1, [5, 6, 7, 8], true);
```

The error handler is there because `exif_read_data()` emits a warning, not an exception, for files without EXIF data, and a missing tag is a normal case. `exif_read_data()` also lives in an optional PHP extension. Declare it, or the import fails on the first host that lacks it:

```json
"require": {
    "php": "^8.3",
    "ext-exif": "*"
}
```

**CMYK JPEGs.** Some files come out of desktop tools in CMYK. Browsers decode CMYK JPEGs, but the colours come out wrong. Since the files are served unchanged, the fix belongs in the data: convert those sources to sRGB once. If you generate derivatives, this is where you would do it instead.

**Inline serving of any type.** The endpoint streams whatever the row points at, with the file's own content type, on the application's origin. For a trusted directory that is acceptable. For user uploads it is not: an SVG or HTML file served inline runs scripts on your origin. Restrict the content type to what the feature needs, or add `Content-Disposition: attachment` for everything else.

## Trade-offs to accept

- The files live outside the Laravel directory layout. Every deployment target has to provide the directory and set `FILES_PATH`. It is one more step in the deploy, and it must be documented, but it is a step that `storage:link` did not need.
- Each first load of a file is a PHP process. The cache header and, if needed, `X-Accel-Redirect` keep that in check, but it is a cost that a static file server does not have.
- Membership is checked per collection, not per file. If some members may see only some files, the form request is where a tighter check would go.
- No S3 yet. When the disk becomes S3, streaming through PHP stops making sense and temporary signed URLs take over. The authorization code stays; the controller's last line changes again.

## Conclusion

Private files need an endpoint, not a symlink. The endpoint has four jobs: check who is asking, check that the file belongs to what they are allowed to see, refuse to build a path from anything that is not a plain file name, and stream the bytes with a private cache header. Everything else, from where the files live to which server sends the bytes, is a configuration detail that can change later without touching the rules. Write the eight tests first; they are the part you will still rely on after the next refactoring.
