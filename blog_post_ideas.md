# Blog post ideas from the annotrAIn image-annotation work

Each idea has a title, the audience it targets, a short description, and the concrete material from the
project that backs it. Pick by audience: the first group is deep technical, the second is architecture
and process.

---

## Deep technical

### 1. Drawing bounding boxes in React without a canvas library

**Audience:** frontend developers.
**Description:** How to build an image annotation editor with an `<img>` and an SVG overlay whose `viewBox`
equals the natural image size, so every rectangle renders with its stored pixel numbers and no scaling
math. Covers pointer capture for draw, move and resize, the "fixed opposite corner" rule for resizing,
`vector-effect="non-scaling-stroke"` for crisp outlines at any zoom, invisible 24 px hit targets over
6 px handles, and why a cancelled pointer gesture must roll back instead of commit.
**Material:** `image-annotation-canvas.tsx`, `box-geometry.ts`, the reducer hook, the review findings that
caught the resize-anchor drift and the `pointercancel` bug.

### 2. Normalized fractions or original pixels? Choosing a coordinate system for image annotations

**Audience:** backend and ML-adjacent developers.
**Description:** The two options, what each one costs when the display size, the zoom level and the stored
image size are all different, and why the project moved from fractions to integer pixels of the original
once it decided to serve originals unscaled. Includes the pointer-to-pixel formula, precision at 1x and 4x
zoom, and what it means for COCO and YOLO exports.
**Material:** spec sections 4.1 and 4.5, the "how will the app store the pixels" discussion, the
`pointFromEvent` helper.

### 3. Serving large private files from Laravel: authorized controller versus public symlink

**Audience:** Laravel developers.
**Description:** A 7 MB camera photo per request through PHP sounds wrong until you compare it with the
alternatives. The post walks through the policy-checked streaming endpoint, path-traversal guards on two
path segments, `Cache-Control: private`, why `storage:link` was rejected, and where `X-Accel-Redirect`
fits when streaming becomes the bottleneck. Ends with the EXIF orientation gotcha and the `ext-exif`
declaration nobody remembers.
**Material:** `DatasetInstanceFileController`, `ShowDatasetInstanceFileRequest`, the `datasets` disk, the
"derivatives or originals" decision.

### 4. Idempotent dataset imports: the index-remap bug that silently corrupts annotations

**Audience:** backend developers working with datasets or any "folder of files to rows" import.
**Description:** A seeder that assigns row indexes from a sorted file list and upserts on `(index, dataset)`
looks idempotent and is not: add one file that sorts earlier and every later row now points at a different
image while the annotations keep their row ids. The fix is match-by-file-name, append-after-max-index,
never rewrite existing rows, plus a metadata back-fill for old rows. With tests that prove it.
**Material:** `DatasetFileImportService`, the service test with the `aaa-new.jpg` case, the migration that
renamed `image_path` to `file_path` without touching row ids.

### 5. Making a Laravel + React CI pipeline three times faster

**Audience:** DevOps-minded developers.
**Description:** From one 7.5 minute job to three parallel jobs finishing in about 2 minutes: PCOV instead
of Xdebug (which was slowing every PHP tool in the job, not only coverage), Pest `--parallel` on an
in-memory SQLite database, caches for Composer, Playwright, Rector and PHPStan results, a composite setup
action, and an aggregate `CI` job for branch protection. Includes the two failures the first runs produced
(`.env` copied after `composer install`; Feature tests needing a Vite manifest) and `withoutVite()` as the
right fix for the second.
**Material:** `ci.yml`, `.github/actions/setup-app`, the per-step timings from the GitHub API.

### 6. Pest browser tests for a drawing UI: synthetic pointer events and the pipe that never closes

**Audience:** PHP developers doing end-to-end tests.
**Description:** How to drive a pointer-based editor from Pest v4 browser tests by dispatching
`PointerEvent`s through `script()`, why `setPointerCapture` needs a try/catch for synthetic events, and
the false "hang" caused by a leaked Playwright server holding a shell pipe open. Short, practical, with the
Playwright version-mismatch error and its one-line fix.
**Material:** `tests/Browser/ImageAnnotationTest.php`, the Task 14 investigation.

### 7. Keyboard access for a mouse-first UI: what an accessibility review actually finds

**Audience:** frontend developers.
**Description:** Not a checklist post. The concrete findings a review made on the editor: focus falling to
`<body>` after Delete, a 12 px hit target, a destructive "Delete all" with no undo, a live region that
announces every pointermove, white chips with invisible focus rings. For each, the fix chosen and the one
still open, so readers see the trade-offs rather than the rules.
**Material:** the review ledger entries for Tasks 11 and 12, the Undo toast implementation.

---

## Architecture, process, team

### 8. Write the decisions down first: a spec with blank "Decision:" lines

**Audience:** tech leads, senior developers.
**Description:** Before writing code, the feature got a spec that listed twelve open decisions, each with
options, costs and a recommendation, and a blank line for the owner. Most were accepted, several were
reversed later (derivatives, coordinates, the import command). The post argues that the value was not the
recommendations but the recorded reasons, which made each reversal a one-paragraph change instead of a
rediscovery.
**Material:** `tasks/image-annotation-spec.md` with its decision lines and reversal notes.

### 9. Name the endpoint for the next feature, not this one

**Audience:** developers and architects.
**Description:** A small post on a small decision with long consequences: the image endpoint became a
dataset-instance *file* endpoint, the content key became `file_path`, the policy became `viewAnnotationFile`,
before any audio or PDF dataset existed. What it cost (one rename pass, a data migration) and what it
avoided (a second endpoint and a second policy later). When to generalize early and when not to.
**Material:** the rename commit, the data migration, the spec's "generic naming" table.

### 10. Seeders, commands, or definition files? Deciding who owns configuration

**Audience:** backend developers and tech leads.
**Description:** The project built an artisan import command, then a `task.json` definition file, then
removed both and went back to seeders within a week. Each step was reasonable; together they show how
"who creates this record in production" is a product decision disguised as a technical one. The post lays
out the three options with their operational costs and ends with the question to ask before building any
of them.
**Material:** the ingestion decision in spec 4.6 and its two reversals.

### 11. Hand-off by `dd()`: splitting a feature between two developers without blocking either

**Audience:** team leads, developers in small teams.
**Description:** The frontend was finished and the backend persistence belonged to another developer. The
solution was a precise seam: the UI posts the real payload to the real route, the backend method dumps it
and nothing else, a test is skipped with the reason, and the spec's last section lists the remaining steps.
A short post on making an incomplete feature safe to merge and easy to pick up.
**Material:** `CoarseDetectionAnnotationTaskService::save()`, spec section 10, the hand-off message.

### 12. Running a feature with AI coding agents: fresh context per task, a reviewer for every change

**Audience:** developers curious about AI-assisted engineering, tech leads.
**Description:** The feature was implemented as 18 tasks, each executed by a fresh agent from a written
brief, each reviewed by another agent against the brief and the project rules, with a fix loop and a ledger
of every ruling. What the reviews caught (resize drift, cancelled gestures, a lost focus, a `dd()` that
would have hidden an error, an endpoint that linked to the wrong task), what they did not, where the human
decisions sat, and what the process cost. Honest about the misses: an instruction the controller gave that
was wrong, a database that was empty when nobody expected it, a browser install that was stale.
**Material:** the SDD ledger, the per-task review reports, the final whole-branch review.

### 13. Reading Figma like a spec: turning layer notes into an interaction list

**Audience:** frontend developers, product-minded developers.
**Description:** The mockups carried the behaviour in Greek layer notes and tooltips, not in a document.
The post shows how the nine frames became a twelve-step interaction list, how placeholder content
("Stone Type", "Granite") was separated from requirements, how the tooltip frame changed the meaning of two
buttons ("Delete all", "Switch palette"), and how the timers were explicitly dropped. A method post for
teams whose designers work in Figma and whose specs live nowhere.
**Material:** spec section 3 and the Figma frames.

---

## Suggested order if you want a series

1 → 2 → 3 → 4 form a coherent "building an image annotation feature" series for developers.
8 → 10 → 11 → 12 form a "how we ran it" series for leads.
5 and 6 stand alone.
