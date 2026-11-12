# Contribution: Add support for Add to button widget

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Amier Davis
**GitHub Username:** amierlorenz
**Issue:** https://github.com/home-assistant/android/issues/6025
**Pull Request:** https://github.com/home-assistant/android/pull/7862

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: making issue-scout my own**

I built the search queries from my fit profile. My strategy originally scanned issues from repos that were brand new, so, I added a star range. Then, some runs would only produce one or two accepted issues which is frustrating because the agent has to start over to find more, so I added a loop mode that grades until 5 issues pass (with a configurable cap that defaults at 15). Lastly, when running multiple times, a lot of duplicate issues from previous runs would show up, so I added functionality for a log of graded issues so I dont regrade issues that were graded within the last 30 days.

**Unit 6: Refining the voice-guard**

The modified voice guard needed a lot of work to feel natural and adhere to oss best practices. For example, the claim-with-a-first-step check had a clause about needing to add a time to the commit message originally. In practice, this does not seem like a very good thing to do. there were also a part of this check that was too strict and would not work in all cases, so I modified this part.

**Unit 7: Troubleshooting the ship-gate**

The final run of ship-gate on my branch all checks passed. Gating opening PRs behind this skill prevented me from submitting code that didn't pass tests. Something that fought me was that my skill diffed against my local main, but it was many commits behind because I pulled my feature branch up to date. Now, it diffs against upstream main.

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I chose this issue becasue it is in a reasonably sized active repo and it fits within my interests and skillset. The skill fell short in a couple different ways, detailed in the toolkit entry below.

https://github.com/home-assistant/android/issues/6025#issuecomment-6515775786

**Toolkit**

- Tool: issue-scout, my search strategy (repo-first queries traced to my fit profile, ranking filters, a star range per run) and my rubric. I ran scans and a loop to find candidates, then graded home-assistant/android issues directly.
- What it produced: accepting #5623 and #6025 in an android home assistant app, each passing every required check and 4 of 5 preferred, with a quoted fact behind every grade. In the same repo it rejected 4 of 13 for recent claims, open PRs, or work the maintainer said to split into many PRs.
- Difficulties: issue search can't see stars, so my first scan surfaced brand-new solo repos. Checks based on names and titles misled me (a commit title, and a `Tile` enum entry that wasn't wired up). Open linked PRs count as live claims even when stalled for months.
- What changed: repo-first search with a star range, an `existing-work` check that reads diffs instead of titles, a graded-issue log so nothing is regraded, and a loop mode. Still to add: a `-linked:pr` search filter and a staleness rule for linked PRs.

---

## Understanding the Issue

**Problem Description**

This is a missing feature, not a bug. In the Home Assistant frontend, an entity's 3-dot menu has an "Add to..." sheet, and the Android app fills its "App actions" section with things it can do with that entity, like adding an entity state widget. PR #5622 introduced the feature and issue #6025 asks to extend it to the Button widget. Today the only way to make a button widget is to start from the widget screen and pick the action and entity by hand, even if you were already looking at that entity.

**Expected Behavior**

When it makes sense for the entity, "Add to..." offers a button widget option. Tapping it opens the button widget configuration screen with the entity I came from already selected. The issue names three changes: a new action in `EntityAddToAction`, logic in `EntityAddToHandler` to offer it "when it makes sense", and `ButtonWidgetConfigureActivity` taking the entity as a parameter and prefilling it, plus updated unit tests. My read of "when it makes sense" is entities with actions of their own, and I asked about it in my claim comment. No maintainer has answered yet.

**Current Behavior**

On Ceiling Lights (`light.ceiling_lights`), the "App actions" section shows only "Add entity state to home screen" and "Add to Android Auto favorites". There is no button widget option, and `ButtonWidgetConfigureActivity` has no way to receive an entity.

**Affected Components**

The issue is out of date on one name: `EntityAddToHandler` no longer exists. Its replacement is `FrontendEntityAddToManager`, after #6792 and #6966 moved the code.

- `app/.../frontend/addto/EntityAddToAction.kt`: the sealed interface of actions. It has no button widget action.
- `app/.../frontend/addto/FrontendEntityAddToManager.kt`: `getActionsForEntity()` decides what to offer by entity domain, and `execute()` maps an action to a `FrontendEvent`.
- `app/.../frontend/navigation/WidgetType.kt`: turns a `NavigateToWidgetConfig` event into the configure screen's intent. It has no button case.
- `app/.../widgets/button/ButtonWidgetConfigureActivity.kt`: has no `newInstance` or `FOR_ENTITY` handling yet. `CameraWidgetConfigureActivity` is the pattern I'm reading from.
- `common/.../data/integration/Entity.kt:504-513`: the domain lists (`DOMAINS_PRESS`, `DOMAINS_TOGGLE`, `APP_PRESS_ACTION_DOMAINS`) and, at line 1240, `isUsableInTile()`.
- `common/src/main/res/values/strings.xml`: the `add_to_*` display strings.
- Tests: `app/src/test/.../frontend/addto/FrontendEntityAddToManagerTest.kt` and `EntityAddToActionTest.kt`.

The thread also has a report that "Add to" only shows for admin users. A maintainer said that belongs to the frontend and fixed it there (home-assistant/frontend#28365), so it isn't part of this work.

**Toolkit**

- Tool: own-it, my change-ownership plugin. I wrote what I didn't know in `.own-it/questions.md` before looking it up, read the code, and closed each question with how I found the answer (read, ran or told).
- What it produced: answered questions like "is `FrontendEntityAddToManager` the same as `EntityAddToHandler`?" (yes, it's the replacement) and "when does it make sense?" (entities with actions of their own), plus a partial system map.
- Difficulties: the issue names a class that no longer exists, so I had to find its replacement before the issue made sense. Filling in `map.md` felt unnatural, so I skipped the recon gate on 2026-10-29 with that reason logged, and the map still has empty sections.
- What changed: `questions.md` became my real understanding tool instead of the map. 15 questions are still open.

---

## Reproduction Process

**Environment Setup**

- OS: macOS 27.0.1 (arm64)
- Project: home-assistant/android at commit `bc40358e40e1e954b96dace0a2f54657e4f86b22`, which was current `main` when I reproduced this on 2026-10-30
- Difference from current `main`: as of 2026-11-03, `main` is `7e2652d6d`, 35 commits later. I didn't re-run the app there, but the code check below gives the same result.
- Build: full flavor, debug, with JDK 21.0.7 to match CI, since this machine defaults to JDK 24
- Device: Android Studio emulator `Medium_Phone`, arm64-v8a, API 36 system image (`google_apis_playstore`)
- Server: Home Assistant 2026.10.4 in Docker (`ghcr.io/home-assistant/home-assistant:stable`) with `demo:` in `configuration.yaml`. The issue says the "Add to" UI needs core 2025.12.0 or later, and this is newer. The container dates from 2026-10-29, before my run.

Challenges: the build expects `google-services.json` files that aren't in the repo, so I copied the mock one from `.github/`. A fresh shell also has no `JAVA_HOME` for JDK 21 and no `ANDROID_HOME`, and the clone has no `local.properties`, so both variables have to be set for command-line Gradle.

**Steps to Reproduce**

Starting from a Mac with Docker and Android Studio installed, and an API 36 arm64 emulator named `Medium_Phone` created in Android Studio's Device Manager:

1. `git clone https://github.com/home-assistant/android.git && cd android && git checkout bc40358e40e1e954b96dace0a2f54657e4f86b22`
2. `for m in app automotive wear; do cp .github/mock-google-services.json $m/google-services.json; done`
3. `export JAVA_HOME=$(/usr/libexec/java_home -v 21) ANDROID_HOME=$HOME/Library/Android/sdk`
4. `$ANDROID_HOME/emulator/emulator -avd Medium_Phone &`, and wait for it to boot.
5. `./gradlew :app:installFullDebug`
6. `docker run -d --name ha-dev -p 8123:8123 -v ~/ha-dev-config:/config ghcr.io/home-assistant/home-assistant:stable`
7. Add a line reading `demo:` to `~/ha-dev-config/configuration.yaml`, then `docker restart ha-dev`. The demo integration provides the Ceiling Lights entity.
8. Open `http://localhost:8123` in a browser and create the first account.
9. Open the app in the emulator, connect to `http://10.0.2.2:8123` (the emulator's address for the host machine), and log in.
10. On the Overview page, under "Summaries", tap the Lights group, then tap Ceiling Lights.
11. Tap the 3-dot menu at the top right, then "Add to...".
12. Observed: "App actions" lists "Add entity state to home screen" and "Add to Android Auto favorites", and nothing for a button widget.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/amierlorenz/android/commit/bc40358e40e1e954b96dace0a2f54657e4f86b22 (the unmodified commit I tested; it's a feature request, so there's no failing code of my own to commit)
- **Screenshots/logs:** a local screenshot from 2026-10-30, 9:45 PM, of the Ceiling Lights "Add to..." sheet on `Medium_Phone`. Its "App actions" section shows the two options above. There's also a code check anyone can run: with `F=app/src/main/kotlin/io/homeassistant/companion/android`, both `git grep -n -i "button" <commit> -- $F/frontend/addto $F/frontend/navigation/WidgetType.kt` and `git grep -n "newInstance\|FOR_ENTITY" <commit> -- $F/widgets/button/ButtonWidgetConfigureActivity.kt` print nothing and exit 1, at `bc40358e4` and at `7e2652d6d`.
- **My findings:** the gap is real at the point the issue names. Every action `getActionsForEntity()` can add is at `FrontendEntityAddToManager.kt:91-105`, and none is for a button widget. For a control, the camera widget path already works: on the demo camera, its configure screen opens with the entity filled in (`FrontendEntityAddToManager.kt:99-100` offers it). That run also showed me a related problem: tapping add with only the prefilled entity gives "Unable to create widget" until I pick the camera from the dropdown. I didn't save a screenshot of the control, so it rests on my notes and those code lines. I decided not to post a repro comment on the issue, so this report lives here.

**Toolkit**

- Tool: repro-check, run in live mode twice on 2026-11-03 on `claim-draft.md` and my repro report against issue #6025.
- What it produced: the first run was `reject`: `specific-modest-claim` and `ai-disclosure` passed, `environment-info`, `clear-repro-steps`, `actual-behavior-matches-issue` and `honest-outcome` failed, and the preferred `control-run` failed. The second run, on the revised report, was `accept`: all six required checks passed, and `control-run` came back `unclear` because the control has no saved output.
- Difficulties: the feature itself was reproduced, but my first draft didn't prove it to a stranger. It left the Home Assistant and Android versions out, said "Spin up Docker Home Assistant instance" with no how and never mentioned the `demo` integration that Ceiling Lights comes from. Its only artifact was a path on my laptop. It also credited the issue with a domain list the issue never states. Fetching before the second run also showed `main` had moved 35 commits past the commit I tested. The second run was graded in the same session that revised the report, so it isn't an independent read.
- What changed: I added the versions and runnable setup steps, copied the sheet's text out of the screenshot, added a `git grep` check anyone can run, called out the difference from current `main`, and quoted the issue directly, marking the domain list as my own suggestion. To finish the control, I'd need to re-run the camera path and save its output.

---

## Solution Approach

**Analysis**

This is a missing feature, and four places in the code leave it out:

- `EntityAddToAction.kt` has no button widget action.
- `FrontendEntityAddToManager.getActionsForEntity()` (lines 90-102) adds widget options by domain and never adds a button one, and `execute()` (line 118) has no case for it.
- `WidgetType.kt` has no `Button` case to build the configure screen's intent.
- `ButtonWidgetConfigureActivity.kt` has no `newInstance` and doesn't read `FOR_ENTITY`.

Prefilling is harder than in the camera widget. `entity_id` isn't a fixed field: the action text watcher (line 120) builds it only when the action box holds a loaded action (lines 126, 165). It clears all fields every time it runs (line 131), takes the value only from a saved widget (lines 144, 168), and runs again when actions and entities finish loading (lines 323, 337).

**Proposed Solution**

Offer a button widget option for entities in `APP_PRESS_ACTION_DOMAINS` (`Entity.kt:512`), including scenes and scripts, which the button widget already supports. Sensors have no actions in their domain, so they don't get it. Automotive and Quest already skip all home-screen widget options.

Tapping it opens the configure screen with an action and the entity filled in, because the entity field only exists once there's an action. I didn't want a hardcoded default for each domain, since those could go stale. My first idea was the alphabetically first action, but that lands on `script.reload` and `scene.apply`, which have no entity field, and "first with a target" lands on `scene.delete`. So once actions load, it picks the first of `press`, `toggle`, `turn_on` the domain offers with a target, and otherwise its first action with a target. The default is only set when the action box is empty, so it can't overwrite what someone typed, and they can still pick any action. A `selectedEntity` property keeps the ID through the watcher re-running. It stays filled in as long as the action is in the entity's domain, and switching server clears it. While the box holds the prefilled action, the dropdown lists every action in that domain.

I'm deliberately not changing:

- Tapping Add with no entity filled in, which is existing behavior in the home-screen flow too.
- The filter that narrows entity IDs by action.
- The camera widget's prefill not counting until it's picked from the dropdown.
- Moving this screen to Compose (#6305).

**Implementation Plan**

1. Decide which files need tests and what those tests are.
2. Write the tests first, in `EntityAddToActionTest.kt`, `FrontendEntityAddToManagerTest.kt` and a new `ButtonWidgetConfigureActivityTest.kt`.
3. Add the action to `EntityAddToAction.kt` and its text to `strings.xml`.
4. Show the option only for `APP_PRESS_ACTION_DOMAINS` and handle it in `execute()`, in `FrontendEntityAddToManager.kt` and `WidgetType.kt`.
5. Add `newInstance` to `ButtonWidgetConfigureActivity.kt` and a `Button` case to `WidgetType.kt`.
6. Prefill the default action and the entity in `ButtonWidgetConfigureActivity.kt`.
7. Add a changelog entry: `ChangelogContent.kt`, `strings_changelog.xml` and the updated screenshot references.
8. Format with ktlint.
9. Run all the tests.
10. Write the PR description.
11. Open the docs PR, updating `docs/integrations/android-widgets.md` with a BETA label, and link the two PRs.

Deviations:

- Step 4 checks `APP_PRESS_ACTION_DOMAINS` directly instead of calling `isUsableInTile()`, since a widget check that says "tile" would read oddly. `EntityWidgetConfigureState` already does the same.
- Skipped running checks after steps 3 and 4, because the code couldn't compile until step 5.
- Step 6 also changed the new test file, adding `delay(1)` to its mocks so they suspend like real server calls.
- Step 6 sets `selectedServerId` early when an entity was passed in, so the prefill isn't skipped if actions load first.
- After trying it on the emulator, I changed "picking a different action clears the entity" to "the entity is only dropped if the action is in another domain", and made the dropdown list the domain's actions while the prefill is in place.
- Added `getSavedWidget()`, which skips the database lookup when adding a widget. It wasn't in the plan, but a load test showed a flaky test caused by waiting on that lookup.

**Toolkit**

- Tool: own-it for the plan, writing my steps and answering questions about them. Direct reads of Home Assistant core in my Docker container, to check what each action accepts.
- What it produced: an 11-step plan and decisions I wouldn't have reached from the code alone, like not using the alphabetical default.
- Difficulties: requiring a `Files:` line on every step felt excessive, and the guard blocked writes to files that aren't code, like my manifest and repro draft. The whole flow got cumbersome enough that I turned own-it off during planning.
- What changed: I logged this in my own-it notes as things to change, and turned it back on to try it for the first build step.

---

## Testing Strategy

**Reproduction, before and after**

Before, at `bc40358e4`: on Ceiling Lights, "Add to..." lists "Add entity state to home screen" and "Add to Android Auto favorites", and no button widget option.

After, repeating the same steps on the built change, these need to be true:

- Ceiling Lights shows the button widget option, and tapping it opens the configure screen with `light.toggle` and `light.ceiling_lights` filled in.
- Office temp (a sensor) doesn't show the option.
- A widget added this way, once tapped on the home screen, changes the light.
- Opening the configure screen from the home screen widget list still shows an empty form.

**Unit tests**

- `EntityAddToActionTest`: the new action's icon, text, `enabled` and `details`.
- `FrontendEntityAddToManagerTest`: a light gets the option, a sensor doesn't, scene and script do, and Automotive and Quest don't. `execute()` returns `NavigateToWidgetConfig` with `WidgetType.Button`.
- `ButtonWidgetConfigureActivityTest`: the entity is still filled in after the action is prefilled and the watcher re-runs, it stays for an action in the same domain, it's dropped for an action in another domain and on a server switch, the dropdown lists the domain's actions, and typed text isn't overwritten.

**The repo's own checks** (`ship-manifest.md`), on the base commit `7e2652d6d` before any change:

| Check | Result |
|---|---|
| ktlint | Pass |
| detekt | Pass |
| Android lint | Pass, no new issues |
| Unit tests | Failed once with 3 of 2,046 failing, then passed on re-run |

The failures were all in `AssistViewModelTest$ListeningChimeTest`, which arrived with #7569 and has nothing to do with this change. It also failed 1 of 5 runs on its own with nothing changed, so it's flaky. Results on my change will be recorded here once it's built.

**Toolkit**

- Tool: my ship manifest from unit 7, with its four checks run in a shell without my usual environment before changing any code.
- What it produced: a baseline on two commits. All four passed on `bc40358e4`. On `7e2652d6d` it took more than one try, as described below.
- Difficulties: a fresh shell has no JDK 21 or Android SDK path, so each row sets both. After updating my branch, tests and lint failed with `Cannot load class Hilt_StopBeaconScanningReceiver`. That was a bad entry in Gradle's local build cache: with the cache off, the build passed. Then the flaky test above appeared.
- What changed: I replaced the one bad cache entry and left the manifest rows unchanged. The flaky test stays in the must-pass row, since excluding it would mean excluding all the unit tests, and I'll re-run if only that test fails.

---

## Implementation Notes

**2026-11-03**

Updated my branch to `7e2652d6d` and re-ran the manifest checks before writing any code. The first run failed tests and lint on a Hilt class that couldn't load. My first guess was stale build output, but cleaning didn't help. Turning the build cache off did, so it was a bad cache entry, and I replaced that one entry. The next run failed on `ListeningChimeTest`, which turned out to be flaky.

Chose the default action. My first idea was the alphabetically first action, but that lands on `script.reload` and `scene.apply`, which have no entity field. "First with a target" lands on `scene.delete`. I settled on a short preference list (`press`, `toggle`, `turn_on`), falling back to the first action with a target, so there are no per-domain values to go stale.

Named the action `ButtonWidget`, matching the widget's class, and wrote the tests first. I predicted all the new and modified tests would fail, but none ran: the build stopped with compile errors because the tests reference `ButtonWidget`, `WidgetType.Button` and `newInstance`, which don't exist yet. A compile error isn't a failing test, since the code never got as far as running.

Then I reworked the tests to match the codebase. In `FrontendEntityAddToManagerTest` I replaced my `assertTrue`/`assertFalse` checks with exact-list `assertEquals`, which is what that file uses. `ButtonWidgetConfigureActivityTest` has no direct equivalent, since no old-style widget configure screen has tests, so I modeled it on `LaunchActivityTest`: the same Robolectric and Hilt setup and Jupiter assertions, with `runTest` instead of `runBlocking`.

Changed the "standard" test to use `weather.test`, which keeps that test's meaning, and added tests for the two classes of press domain: vehicle and non-vehicle. Switched the icon to `mdi:gesture-tap` after finding that every other "Add to" widget matches its icon in the widget menu, and mine didn't.

Added the action, its string, the domain check, `execute()`, `WidgetType.Button` and `newInstance`. Skipped running checks after steps 3 and 4, because nothing could compile until step 5. The first real run compiled, with 71 tests completed and 4 failing, as I predicted. All 4 failures are the default-action tests in `ButtonWidgetConfigureActivityTest`, because prefilling doesn't exist yet. Installed that build, and the option showed up on Ceiling Lights and opened an empty form.

Added the prefill in `ButtonWidgetConfigureActivity`. `prefillDefaultAction()` runs from `setAdapterActions()`, so it happens once actions have loaded. `prefilledEntityFor()` gives the watcher the entity only while the action box still holds the prefilled action, and `onServerSelected()` clears it. The check passed with all 71 tests, as I predicted. Two things came up that weren't in the plan. `selectedServerId` could still be empty when actions finish loading, which would skip the prefill, so the activity now sets it early when an entity was passed in. The test mocks also answered instantly, while real server calls suspend, so I added `yield()` to them. No test in `app` uses `yield()`, so I later switched to `delay(1)`, the pattern `FrontendEntityAddToManagerTest` already uses.

Tried it on the emulator. Ceiling Lights opened with `light.toggle` and its entity filled in, as I predicted. The widget changed the light when tapped, and opening the form from the widget list stayed empty. Two things felt wrong in actual use, neither of which the unit tests could have told me. The dropdown only listed `light.toggle`, since it filters on the text in the box, so people couldn't see their other choices. Changing the action also cleared the entity, even for another action on the same light. So I changed the plan: the entity stays as long as the new action is in the same domain, and while the box holds the prefilled action, the dropdown lists every action in that domain. I left `homeassistant.*` actions dropping the entity even though they'd work on it, since keeping it would add a condition for a rare case.

**2026-11-04**

Both changes worked on the emulator as I predicted: the dropdown listed the other light actions, and `light.turn_on` kept the entity. One test failed with `expected: <light.ceiling> but was: <[light.ceiling]>`. It wasn't a bug in the feature. `entity_id` can be a list because one action can target several entities, and `WidgetDynamicFieldAdapter` turns field text with a comma into a list. A field drawn for the first time still holds a string, so a test had to know which path the field took. I rejected converting the value to a string and stripping the brackets, since that would hide the real shape. Instead, the test helper `pendingEntityIds()` returns a list either way, so every test asks which entities the widget would act on. The check passed 10 runs out of 10 with the earlier version of the helper.

I didn't want to add a flaky test to the project's CI, so I repeated the 10 runs with `pendingEntityIds()` and all 12 CPU cores kept busy. I predicted it would work, but it failed 3 of 10 runs, all on the same-domain test, with `expected: <[light.ceiling]> but was: <null>`. It had passed every run on an idle machine, so this test can be flaky, and it needs fixing before the PR.

The cause looked like a race: the watcher clears the fields, then waits on a Room query on a background thread before rebuilding them, and the test could read the value during that wait. But when adding a widget there's no widget ID, so that query can never find anything. I added `getSavedWidget()`, which skips the query in that case. That removes the wait instead of working around it in the test, and it shrinks the same gap in the real app. The same 10 runs under the same load then passed 10 out of 10, as I predicted. That's strong evidence but not proof.

Then the app started crashing on the emulator when opening the Home Assistant screen: a native `SIGSEGV` inside WebView 133 (`libmonochrome_64.so`), about half a second after the frontend started loading. A clean boot and reinstall didn't help, though I'd predicted it would. To find out whether my change caused it, I built unmodified `main` at `7e2652d6d` in a separate worktree, and it crashed the same way, with the same fault address. So it isn't caused by my change. I turned own-it off for this troubleshooting, since these checks weren't testing my understanding of the code.

**2026-11-10 and 11**

Updated my branch to upstream `c6eb57c26` (41 new commits), and my work reapplied with no conflicts. The first test run died because the disk was full. After freeing space, the same 73 tests passed, as I predicted. That only covers the tests matching my `--tests` filters, so it doesn't prove upstream changes elsewhere left the feature alone.

Added the changelog entry: "Added **action button widgets** to the Add to menu of supported entities", in the `new` list, app only, opening the button widget configuration. I first included "(Home Assistant 2025.12+)". The contributor docs say nothing about versions in entries, and the only entry in the file's history with one (Matter sharing, #7517) needed the newest release at the time. 2025.12 is about a year old, so I dropped it.

The changelog has a screenshot test, so I regenerated its reference images. The first run of the update task regenerated every screenshot in `:app` and failed on two unrelated screens (Matter commissioning and connection error). It changed 146 images, but only 4 were the changelog. The other 142 were my Mac rendering slightly differently from the Linux CI host, which the contributor docs' screenshot testing page says not to chase locally, so I restored them. I predicted all 6 changelog images would change, but only 4 did: on the two landscape sizes, my entry is below the visible area. Running only `ChangelogScreenshotTest` with `--tests` changed the same 4, as I predicted. Merged fork PRs #7655 and #7569 also committed only their own images, and maintainers pushed screenshot fixes to the contributor's branch.

Formatted with ktlint. I predicted it would change many files, but it changed only the new test file. The other files followed code already in them, and the test file had no close example to copy. I couldn't see what it changed, because the file was untracked and git had no earlier version to compare against.

Reviewed the change against the project's Kotlin style guide and kept two things a reviewer might question. The `", "` that `prefilledEntityFor()` appends isn't a named constant, but it copies the format an existing line in the same file uses to load saved widgets, so I left both alone instead of changing upstream code. The test mocks `Server` instead of building a real one; the testing guide prefers real objects, but 22 test files mock it and 19 build it, so either is accepted here. Long test names are fine: `.editorconfig` turns off the 120-character limit for test files, and 591 test names in the repo are longer.

Ran the manifest checks. ktlint passed, as I predicted. I predicted detekt would pass too, but it failed with two issues in my code. ktlint only checks formatting, while detekt checks structure. `getActionsForEntity()` reached a complexity of 15 against a limit of 14, because my button widget `if` added a path to a function already at the limit. I moved the widget choices into their own function, `widgetActionsFor()`, since together they do one job: decide which widgets an entity gets when it isn't in a car or on a Quest. `prefillDefaultAction()` had 3 `return`s against a limit of 2, so I merged the two early exits into one `if` that also checks for a missing entity. That needed a local copy of `selectedEntity`, because Kotlin won't treat a `var` property as non-null after a null check. I left screenshot validation out of my local checks: I had just regenerated the changelog images, and the Mac renders differently from CI, so CI's run on the PR is the one that counts.

After those fixes, ktlint, detekt and lint passed, but the unit tests wouldn't compile: `AppSensorModule.kt` couldn't find `GeneratedProvidesSensorApp`, a class no one writes. A code generator (KSP) builds it from every sensor marked `@ProvidesSensor`. After my edits, KSP ran incrementally, looking only at the three files I'd changed. None of them has a sensor, and the generator writes no file when it finds none, so the class disappeared. Rerunning that one KSP task from scratch brought the file back for one build variant, so I cleared Gradle's build cache and ran again, predicting it would pass. It failed the same way four more times, because Gradle remembers past builds in three places: the `build/` folders, the build cache, and its own task history in the background daemon and the project's `.gradle/` folder. Each cleanup I tried cleared only some of them, and once even put the broken result back from the cache. To find out whether my code was the cause, I ran the same tests on upstream `main` from scratch and after a comment-only edit, and both passed. Then I ran my branch from scratch with all three cleared, and all 3,152 unit tests passed. CI always starts from a fresh checkout, so it can't hit this. I couldn't reproduce the lost file on `main` with a comment edit, so I haven't reported it upstream. I also found that CI moved to JDK 25 on 2026-10-26 (#7703), while I've been running JDK 21. Every check passes on 21, and CI will run 25 on the PR.

**2026-11-11 (evening)**

Rebuilt and installed the app after the detekt refactor, predicting everything would work as before. It did: on the emulator, Ceiling Lights' "Add to..." sheet lists "Add entity action to home screen", and tapping it opens the configure screen with `light.toggle` and `light.ceiling_lights` filled in. I took these as the PR's four screenshots, in light and dark mode, switching with `adb shell cmd uimode night yes/no`.

**Toolkit**

- Tool: own-it in the execute phase. I gave a prediction before every check, ran each check through `own-it run`, and explained the result before getting hints. The ship manifest's four checks did the verifying, and Claude Code (Claude Opus 5.5) made the edits I decided on.
- What it produced: a log of each prediction next to its outcome, and two fixes I chose myself for detekt's complexity and return-count findings.
- Difficulties: own-it's guard blocks edits outside the current step's files, so I had to add files to steps mid-way (the screenshot path after #7800, the detekt fixes, `ship-manifest.md`). Its hint ladder unlocked as the KSP runs kept failing, but the agent's own explanations were wrong twice (build cache only, then `--rerun`), which cost me extra runs before the real cause was found.
- What changed: I now check that a "clean" build is really clean (daemon, `.gradle/`, build cache, `build/`) before trusting a result, and I compare against upstream `main` when a failure doesn't touch my files. The manifest now records the JDK 25 gap and the from-scratch recipe.

---

## Pull Request

**PR Link:** https://github.com/home-assistant/android/pull/7862 ("Add the action button widget to the Add to menu", opened 2026-11-11). Documentation PR: https://github.com/home-assistant/companion.home-assistant/pull/1475.

**PR Description:** The PR follows the repo's template. The summary links #5622 and closes #6025, then lists what changed: the option appears for entities in `APP_PRESS_ACTION_DOMAINS`; the configure screen opens prefilled with the entity and a default action; the entity stays for actions in the same domain; the dropdown lists all of the domain's actions while the default is in place; the widget-list path is unchanged; and `getSavedWidget()` (`ButtonWidgetConfigureActivity.kt:236`) skips the saved-widget lookup when adding a widget, which fixed a flaky test (3 of 10 failures under load before, 10 of 10 passes after). Validation lists the emulator test, the 3,152 unit tests with ktlint, detekt and lint, and what the new `ButtonWidgetConfigureActivityTest` covers. Screenshots show the "Add to" sheet before, and the sheet and prefilled configure screen after, in light and dark. It links the documentation PR, and the notes say I used Claude Code with Claude Opus 5.5 to write the code from my plan. The branch has four commits: the `getSavedWidget()` change, the prefill with its test, the "Add to" option with its tests, and the changelog entry with its screenshot references.

**Maintainer Feedback:**

- 2026-11-11: Copilot's automatic review said the changelog entry changed "without updating the six screenshot references." The CLA bot asked me to sign the Contributor License Agreement. No maintainer has commented yet.
- 2026-11-11: I signed the CLA (the PR now has the `cla-signed` label). Copilot was wrong: the PR updates 4 of the 6 changelog references, and the other two don't change. I replied on its thread that the foldable, phone, tablet and tablet_landscape references are included, and that on small_phone and phone_landscape the entries above mine fill the visible area. #7607 got the same comment and answered it the same way.

**Status:** Awaiting review

**Toolkit**

- Tool: pr-precheck, run in live mode on 2026-11-11 on `pr_draft.md` and the uncommitted branch diff (14 files, +429/-19) against `plan.md`, my claim comment and issue #6025.
- What it produced: `reject`, with 1 of 4 required checks passing. reviewable passed: no debris, `git diff --check` is clean, and the only added comments explain why. no-drift failed: `plan.md` still says "Deviations: none yet", and the description doesn't mention `getSavedWidget()` skipping the database lookup or the server being set early, which the plan never named. is-tested failed: the draft says "I manually tested in an emulator" without what I observed, and there's no before/after (the repro comment was never posted, so the before lives only in this story). conforms-to-repo-standards failed: the Screenshots section and the documentation PR number are still empty. The AI disclosure is present. Voice notes: "I manually tested" breaks no-claims-without-evidence. transparent is met ("This is my first contribution to this repository").
- Difficulties: the rubric's no-drift and is-tested checks expect a plan comment and a repro comment on the issue thread, and I posted neither. The scope file says the plan isn't posted and deviations go in the PR description, so I graded against `plan.md`. The repro evidence has to come from the description itself.
- What changed: I filled in `plan.md`'s deviations, added the `getSavedWidget()` bullet to the description, wrote `test_evidence.md` with a before screenshot from `main`, and added placeholders for the screenshots and the documentation PR number. Through the ship-gate, the precheck then rejected the committed branch because my local `main` was 75 commits behind, so I fast-forwarded it and changed three rubric checks that asked for things this repo doesn't do (see Unit 7). The next run passed, and the voice guard then stopped `gh pr create` twice before the PR opened (see Unit 6).

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

Draft bullets to rewrite in my own words:
- Kotlin: the Elvis operator (`?:`, including `?: return`), smart casts and why a `var` property needs a local `val` copy, sealed types with exhaustive `when`.
- Android: passing data to an activity with an `Intent` extra (`FOR_ENTITY`) through a `newInstance` factory, Robolectric + Hilt tests for an activity, and why a test needed `delay(1)` mocks to behave like real server calls.
- Gradle and KSP: incremental code generation, and the three places a build remembers results (`build/`, the build cache, the daemon and `.gradle/`). `UP-TO-DATE` vs `FROM-CACHE`.
- Tooling: ktlint checks layout, detekt checks structure (complexity, return count); screenshot tests and why reference images differ between macOS and Linux.

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

Draft bullets to rewrite:
- The flaky same-domain test (3/10 failures under load), fixed by removing the wait (`getSavedWidget()`) instead of patching the test.
- The WebView crash on the emulator, proven not mine by building clean `main`.
- detekt's two findings, fixed with my own approaches (split out `widgetActionsFor()`, merge the early returns).
- The missing `GeneratedProvidesSensorApp`: five failed runs, then comparing against `main` and a full from-scratch run.

**What I'd Do Differently Next Time**

[Reflection on your process.]

Draft bullets to rewrite:
- Check CI's real setup (`.github/workflows/pr.yml`, `setup-build-env`) at the start, not only the docs; I'd have caught JDK 25 earlier.
- Make sure a "clean" build is really clean before trusting a failure, and compare against upstream `main` sooner.
- Register new files with git (`git add -N`) before running a formatter, so I can see what it changed.

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]

Draft entries (keep the ones that really helped):
- https://contributors.home-assistant.io/companion/android/submitting/: the PR template rules, the changelog and draft-PR guidance.
- https://contributors.home-assistant.io/companion/android/screenshot-testing/: "When CI disagrees with your machine", which is why I restored the 142 unrelated images.
- https://github.com/home-assistant/android/pull/5622: the original "Add to" PR whose camera-widget path I copied. Its docs PR, home-assistant/companion.home-assistant#1232, is the model for mine.
- https://github.com/home-assistant/android/pull/7569 and https://github.com/home-assistant/android/pull/7655: merged fork PRs I modeled my screenshots and AI disclosure on.
- The repo's `.agents/skills/` (Kotlin style, testing): the style review and the screenshot update command.
