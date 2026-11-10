## What this changes

<!-- One or two sentences. Link the issue it fixes, e.g. "Closes #123". -->

## Checklist

- [ ] In `packages/wherefrom`: `dart format --output=none --set-exit-if-changed .` and `flutter analyze` are clean, `flutter test` passes
- [ ] `dart test` passes in `packages/wherefrom_builder` if it changed
- [ ] Closes an issue labelled `accepted`; at most 400 changed lines; no `// ignore:`, `@Suppress(` or `swiftlint:disable` (checked)
- [ ] Changes to Dart, Kotlin or Swift sources come with a test (checked)
- [ ] If this fixes a bug: there is a test that fails without the fix
- [ ] If this touches native code: the example builds (`flutter build apk --debug`, `flutter build ios --simulator --no-codesign`)
- [ ] User-visible change: `CHANGELOG.md` entry, and README or TESTING.md updated
- [ ] Linked issue:

### AI use

<!-- Using AI is welcome. Please tick one, naming the tool and model if you used AI; a reminder appears if it is missing. See CONTRIBUTING.md. -->

- [ ] None
- [ ] Some (tool or agent, and model: ...)
- [ ] Most (tool or agent, and model: ...)
- [ ] I have read and tested every line.
