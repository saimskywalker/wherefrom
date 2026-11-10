# Contributing

Issues and pull requests are welcome, especially device reports
("Play returned X on <OEM> Android N"), since referrer behaviour varies by
vendor and Play Store version.

The repository holds two packages: the Flutter plugin in
`packages/wherefrom` and the pure-Dart builder in
`packages/wherefrom_builder`. Run the checks CI runs for the plugin:

```bash
cd packages/wherefrom
dart format --set-exit-if-changed .
flutter analyze
flutter test
(cd example && flutter build apk --debug)
(cd example/android && ./gradlew :wherefrom:testDebugUnitTest)
(cd example && flutter build ios --simulator --debug --no-codesign)
```

CI also builds release versions. To run the Android checks locally (from the
repository root, with an emulator running for the last one):

```bash
(cd packages/wherefrom/example && flutter build appbundle --release && flutter build apk --release)
python3 .github/scripts/merged_manifest_snapshot.py   # --update to accept a manifest change
python3 .github/scripts/check_native_libs.py packages/wherefrom/example/build/app/outputs/bundle/release/app-release.aab
.github/scripts/android_release_smoke.sh packages/wherefrom/example/build/app/outputs/flutter-apk/app-release.apk
```

and on iOS:

```bash
(cd packages/wherefrom/example && flutter build ios --release --no-codesign)
.github/scripts/check_ios_privacy_manifest.sh packages/wherefrom/example/build/ios/iphoneos/Runner.app
```

The pure-Dart builder in `packages/wherefrom_builder` has its own CI job
(Dart 3.8.0 and stable):

```bash
cd packages/wherefrom_builder
dart pub get
dart format --output=none --set-exit-if-changed .
dart analyze --fatal-infos
dart test
dart run example/wherefrom_builder_example.dart
```

To see a real referrer on a device, follow
[TESTING.md](packages/wherefrom/TESTING.md).

## How changes land

Every change reaches `main` through a pull request that the maintainer
reviews and merges, including changes from forks. Every pull request, small
fixes included, needs an issue that a maintainer has labelled `accepted`
first, so the approach can be agreed before you spend time on it.

## Pull requests

Every CI check must pass before a pull request can be merged. The
`PR rules` check enforces these:

- **Issue first.** Open an issue and wait until a maintainer labels it
  `accepted`, then write `Closes #N` in the pull request.
- **Tests.** Every change to the Dart, Kotlin or Swift sources of either
  package (or their examples) needs a test change: the package's `test/`,
  `packages/wherefrom/android/src/test/`, or the example's tests. A bug fix
  needs a test that fails without the fix.
- **Small.** At most 400 changed lines (lock files not counted), one issue
  per pull request.
- **No suppressions.** No `// ignore:`, `@Suppress(`, `swiftlint:disable` or
  `# shellcheck disable`, no changes to any `analysis_options.yaml` or
  `.shellcheckrc`. Fix the finding.
- **At most 2 open pull requests** per person.

If a rule really does not fit your change, say why in the description. A
maintainer can waive it with a label (`no-tests-needed`, `large-change` or
`suppression-ok`).

## Legal notice

By submitting a contribution you confirm that you wrote it, that you have the
right to submit it, and that it may be distributed under the project license
(see [LICENSE](LICENSE)).

## Ground rules

- **No Kotlin Gradle Plugin.** The Android plugin uses AGP built-in Kotlin;
  CI fails if the plugin's `android/build.gradle.kts` references KGP.
- **Shared vectors.** Parsing and building rules live in
  `packages/wherefrom/test/vectors/*.json` and are read by
  both packages, so the plugin and the builder cannot drift apart. A change
  in behaviour is a change to the vectors.
- **Never throw on input.** Referrer and deep link parsing return a typed
  result for malformed strings; a fix comes with a vector for the string that
  broke it.
- **No real data.** Fixtures and examples use invented package names,
  campaigns and domains such as `com.example.app`.

## AI-assisted contributions

Using AI tools is fine; please just be open about it.

- Say in the PR description which AI tools you used, if any: none, some or
  most, plus the tool or agent and the model (for example "Claude Code,
  Opus 5.5" or "Copilot, GPT-5").
- Please write the PR description in your own words; short and plain is fine.
  It shows us what you understood about the change. Please don't paste
  generated text.
- Read, run and test every line you submit, and be ready to explain it in
  review. If you don't understand what a line does, don't submit it.
- Keep the change focused. Agents like to add unrelated "improvements",
  long comments and dead code; remove them before you open the pull request.
- Pull requests that are raw, unreviewed agent output will be closed.
- If you use an agent, point it at [AGENTS.md](AGENTS.md).
- If the disclosure is missing, a check adds a reminder to the PR. It does
  not fail the PR, but a maintainer will ask about it before merging.

## Commits

One focused change per pull request. Commit subjects are a single line that
says what changed, either plain (`claimOnce(): one in-process claim ...`) or
with a type prefix (`docs: ...`, `fix: ...`). Add an entry to the package's
`CHANGELOG.md` for anything its users will notice. Releases follow
[RELEASING.md](RELEASING.md).

If this plugin saved you a day of chasing referrers, a star on the
repository helps other Flutter developers find it.

## Stay in touch

Don't forget to follow [@saimskywalker](https://github.com/saimskywalker) on
GitHub to see new releases and other open-source projects as they come out.
