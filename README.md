# `rnx-cli` bundler bug reproducer

to reproduce:

- run `yarn rnx-cli bundle --dev false`; observe the import error
- set `treeShake` to `false` (`package.json` line 60) and rerun the same command; there's an unrelated error (see the section below) but it works

## side note

since the switch to Hermes v1, I've had an issue where it fails to find the Hermes compiler. I fixed this for my app with the following patch:

```diff
diff --git a/lib/bundle/hermes.js b/lib/bundle/hermes.js
index eeaf82d963796cd7fef059e9731229c3d584e383..08eeb89853fabc8c80cf63490a6338a050621092 100644
--- a/lib/bundle/hermes.js
+++ b/lib/bundle/hermes.js
@@ -58,6 +58,8 @@ function findHermesBinary({ reactNativePath }) {
     const locations = [
         () => path.join(reactNativePath, "sdks", "hermesc"),
         () => (0, package_1.findPackageDependencyDir)("hermes-engine"),
+        // PATCH: also look for the newer hermes-compiler package (used for Hermes v1)
+        () => (0, package_1.findPackageDependencyDir)("hermes-compiler/hermesc"),
     ];
     for (const getLocation of locations) {
         const location = getLocation();

```

if this looks okay, let me know and I'll open a PR.