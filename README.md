# NPM Publish Wildcard Regression Bug

This project / repo is a minimal reproduction of a npm publish bug first
occurring in `v6.12.0`

The format of theis project is as follows:
```
|-- index.js
|-- package.json
`-- lib/
    |-- other/
    |   `-- *
    `-- */
        `-- file.js
```

It appears that in versions of NPM > `6.12.0`, the `lib/other/*` textfile
 and the 'lib/*/file.js' files will not be added to the final artifact
 when running `npm publish`. This is bad - since the files will be present
 during CI, it is very easy to have a green build and release broken artifacts.

The contents of the final artifact are evaluated below using `npm pack`.

Adding entries manually to the `files` array in `package.json` does not
fix the issue.
```
  ...
  "files": ["lib/other/*"],
  ...
```

#### Environment
```bash
$ node -v
v12.16.0
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ uname
Darwin
```

#### Behavior for NPM v6.12.0

```bash
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm -v
6.12.0
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm pack --dry-run
npm notice
npm notice 📦  npm-bug-repro@1.0.0
npm notice === Tarball Contents ===
npm notice 25B   lib/other/*
npm notice 57B   lib/*/file.js
npm notice 61B   index.js
npm notice 273B  package.json
npm notice 2.7kB README.md
npm notice === Tarball Details ===
npm notice name:          npm-bug-repro
npm notice version:       1.0.0
npm notice filename:      npm-bug-repro-1.0.0.tgz
npm notice package size:  1.2 kB
npm notice unpacked size: 3.1 kB
npm notice shasum:        87c035e2eda0c570f202fab45bc2c8188f674290
npm notice integrity:     sha512-U2rzmNTh+E/zE[...]/Nu84fGo8oDog==
npm notice total files:   5
npm notice
npm-bug-repro-1.0.0.tgz
```

#### Behavior for NPM v6.12.1
```bash
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm -v
6.12.1
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm pack --dry-run
npm notice
npm notice 📦  npm-bug-repro@1.0.0
npm notice === Tarball Contents ===
npm notice 61B   index.js
npm notice 273B  package.json
npm notice 1.1kB README.md
npm notice === Tarball Details ===
npm notice name:          npm-bug-repro
npm notice version:       1.0.0
npm notice filename:      npm-bug-repro-1.0.0.tgz
npm notice package size:  877 B
npm notice unpacked size: 1.5 kB
npm notice shasum:        fb794f492e66b9606c6c33b6e243021cbc4561df
npm notice integrity:     sha512-CwTMtMdn9u/HA[...]m2Ix2mp88A0Bw==
npm notice total files:   3
npm notice
npm-bug-repro-1.0.0.tgz
```

#### Behavior for NPM v6.14.4 (latest)
```bash
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm -v
6.14.4
mgerber@mgerber-ltm ~/work/npm-bug-repro
$ npm pack --dry-run
npm notice
npm notice 📦  npm-bug-repro@1.0.0
npm notice === Tarball Contents ===
npm notice 61B   index.js
npm notice 273B  package.json
npm notice 1.9kB README.md
npm notice === Tarball Details ===
npm notice name:          npm-bug-repro
npm notice version:       1.0.0
npm notice filename:      npm-bug-repro-1.0.0.tgz
npm notice package size:  1.0 kB
npm notice unpacked size: 2.3 kB
npm notice shasum:        61dc7974fe121ffc16cd700ea8f8a9bedaddaf70
npm notice integrity:     sha512-/iBPuwRHTSZg8[...]lXau4+ER6MfJQ==
npm notice total files:   3
npm notice
npm-bug-repro-1.0.0.tgz
```
