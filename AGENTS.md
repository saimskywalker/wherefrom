# AGENTS.md

Rules for AI coding agents working in this repository. People should read
[CONTRIBUTING.md](CONTRIBUTING.md); everything here applies to them too. The
`PR rules` check enforces the rules marked (checked); a pull request that
breaks one cannot be merged.

## What wherefrom is

Two packages that share one set of test vectors:

- `packages/wherefrom`: a Flutter plugin that reads the Play Install Referrer
  (Kotlin, `android/src/main/kotlin/`) and the Apple AdServices token (Swift,
  `ios/wherefrom/Sources/wherefrom/`) and turns it into a typed result and
  the deep link the user wanted (`lib/src/`).
- `packages/wherefrom_builder`: pure Dart, no runtime dependencies; builds
  referrer strings and Play Store URLs on a server.

Read before you touch:

- parsing or building: the "Shared vectors" and "Never throw on input" rules
  in CONTRIBUTING.md, `packages/wherefrom/doc/decoding.md` and
  `packages/wherefrom/test/vectors/*.json`, which both packages' tests read.
- `readOnce()`, `claimOnce()` and the stored state:
  `packages/wherefrom/doc/consume-once.md`.
- Android build setup: the "No Kotlin Gradle Plugin" rule in CONTRIBUTING.md
  and the comment at the top of `packages/wherefrom/android/build.gradle.kts`.
- what real devices return: `packages/wherefrom/TESTING.md`.
- releases: `RELEASING.md`.

## Commands

CI uses Flutter 3.47.7 and Java 17. Every one of these must pass before you
open or update a pull request. CI runs the same ones.

The plugin, in `packages/wherefrom`:

```bash
flutter pub get
dart format --output=none --set-exit-if-changed .
flutter analyze
flutter test
(cd example && flutter test)
dart pub publish --dry-run
(cd example && flutter build apk --debug)
(cd example/android && ./gradlew :wherefrom:testDebugUnitTest)
(cd example && flutter build ios --simulator --debug --no-codesign)
```

The builder, in `packages/wherefrom_builder` (Dart 3.8.0 and stable):

```bash
dart pub get
dart format --output=none --set-exit-if-changed .
dart analyze --fatal-infos
dart test
dart run example/wherefrom_builder_example.dart
dart pub publish --dry-run
```

CI also builds and checks release builds (R8 bundle, merged manifest, 16 KB
alignment, an emulator smoke test, the iOS privacy manifest); CONTRIBUTING.md
lists the commands to run them locally.

Run one test while iterating: `flutter test test/install_referrer_test.dart`
(plugin), `dart test test/play_store_url_test.dart` (builder), or
`./gradlew :wherefrom:testDebugUnitTest --tests 'io.github.saimskywalker.wherefrom.ReferrerReaderTest'`
(in `packages/wherefrom/example/android`).

## Stop and ask the human

Some things must come from the real world. An agent must never invent them.
Stop and ask the person you work for to provide them:

- **Real Play or Apple output.** A referrer string, response code, install
  source or AdServices answer described as "what Play returns" or "what the
  device reports" comes from a real install, with the device, OS version and
  Play Store version (see TESTING.md). Never write one from memory or guess
  how an OEM or Play Store version behaves.
- **API behaviour.** Cite the Play Install Referrer, Android or Apple
  AdServices documentation or the source that explains a behaviour. If you
  cannot find one, say so; do not guess.
- **Release build results.** The merged-manifest snapshot
  (`doc/merged-manifest.snapshot.xml`), R8, 16 KB alignment and privacy
  manifest results come from a real release build. Never update the snapshot
  without running `merged_manifest_snapshot.py --update` after one.
- **An accepted issue.** If the change has no issue labelled `accepted`, stop
  and ask the human to open one and wait for a maintainer.

## Code

- **English only** for code, identifiers, comments, commit messages and docs.
  Answer the person you work for in their language.
- Target the minimum versions in the manifests: the plugin Dart 3.12 and
  Flutter 3.44 (`packages/wherefrom/pubspec.yaml`), the builder Dart 3.8;
  Android minSdk 21 and JVM 17 with AGP built-in Kotlin
  (`android/build.gradle.kts`); iOS 15.0 (`ios/wherefrom.podspec`,
  `ios/wherefrom/Package.swift`). Do not use features newer than that.
- Fixtures and examples use invented package names, campaigns and domains
  such as `com.example.app`. Never use real data.
- Comments are sparse: only where the code is not self-evident. Do not add
  comments, doc comments or type annotations to code you did not change.

## Tests (checked)

- Every change to the Dart, Kotlin or Swift sources of either package (or
  their examples) comes with a test change: `test/` of that package,
  `packages/wherefrom/android/src/test/`, the example's `test/`,
  `integration_test/` or `ios/RunnerTests/`. A bug fix has a test that fails
  without the fix; check that it does before you open the pull request.
- A change in parsing or building behaviour is a change to the shared
  vectors; a parser fix adds a vector for the string that broke it.

## Never

- Never silence the analyzer or a linter (checked): no `// ignore:`,
  `// ignore_for_file:`, `@Suppress(`, `swiftlint:disable`,
  `# shellcheck disable`, no changes to any `analysis_options.yaml` or
  `.shellcheckrc`. Fix the finding instead. The existing
  `@Suppress("DEPRECATION")` fallbacks for old Android versions stay; a new
  one needs a maintainer's waiver.
- Never delete, skip, loosen or rewrite an existing test, expectation or
  vector to make CI pass. If a test looks wrong, say so in the issue.
- Never change what users depend on without an issue that asks for it: the
  exported API in `lib/wherefrom.dart` and `wherefrom_builder.dart`, the
  method channel `io.github.saimskywalker/wherefrom`, the
  `UnavailableReason` names, and the stored state file names
  (`imaji_install_attribution.handled`, `.failed_reads`, `.first_seen`).
- Never apply the Kotlin Gradle Plugin, or give `wherefrom_builder` a
  runtime dependency.
- Never bump a version (`pubspec.yaml`, `android/build.gradle.kts`, the
  podspec), edit a released `CHANGELOG.md` section, publish, tag or release.
  Add your entry at the top of the package's `CHANGELOG.md` under
  `## Unreleased` (create the heading if it is missing).
- Never add a dependency or change CI workflows or `.github/scripts/` unless
  the issue asks for it.
- Never add unrelated "improvements": no drive-by refactors, renames,
  reformatting of untouched code or dead code.
- Never open pull requests, issues or comments on your own. The human reviews
  your work and posts it.

## Pull requests (checked)

- First an issue, labelled `accepted` by a maintainer. The pull request says
  `Closes #N`.
- One issue per pull request, at most 400 changed lines.
- At most 2 open pull requests per person.
- Fill in the pull request template, including `### AI use` with the tool and
  model.
- The person submitting has read, run and understood every line, and writes
  the description in their own words. Pull requests that are raw, unreviewed
  agent output will be closed.
