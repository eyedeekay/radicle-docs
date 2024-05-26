---
title: Download Radicle
subtitle: Download a Radicle release
layout: page
variant: wide
---
This is Radicle's <strong class="highlight">download</strong> page.
Radicle uses a [reproducible build][rb] pipeline to ensure that anyone can
reproduce the binaries on this page from source.

[rb]: https://reproducible-builds.org/

<p id="release" class="loading">
  <span class="release-loader"></span>
  <span>
    <h2 id="release-name">Latest release</h2>
    <p id="release-info"><!-- Dynamic --></p>
  </span>
</p>

<noscript>
  <p>
    Browse the latest release at
    <a href="https://files.radicle.xyz/releases/latest">https://files.radicle.xyz/releases/latest</a>
  </p>
</noscript>

<table class="hidden loading" id="releases">
  <thead>
    <th scope="col">OS</th>
    <th scope="col" class="desktop">Arch</th>
    <th scope="col">File</th>
    <th scope="col">Signature</th>
    <th scope="col">Checksum</th>
  </thead>
  <tr data-release-arch="x86_64" data-release-binary="unknown-linux-musl">
    <th scope="row" rowspan="2">Linux</th>
    <td class="release-arch desktop">Loading…</td>
    <td>💾 <a class="release-url">Loading…</a></td>
    <td><a class="release-sig">Loading…</a></td>
    <td><a class="release-checksum">Loading…</a></td>
  </tr>
  <tr data-release-arch="aarch64" data-release-binary="unknown-linux-musl">
    <td class="release-arch desktop">Loading…</td>
    <td>💾 <a class="release-url">Loading…</a></td>
    <td><a class="release-sig">Loading…</a></td>
    <td><a class="release-checksum">Loading…</a></td>
  </tr>
  <tr data-release-arch="x86_64" data-release-binary="apple-darwin">
    <th scope="row" rowspan="2">macOS</th>
    <td class="release-arch desktop">Loading…</td>
    <td>💾 <a class="release-url">Loading…</a></td>
    <td><a class="release-sig">Loading…</a></td>
    <td><a class="release-checksum">Loading…</a></td>
  </tr>
  <tr data-release-arch="aarch64" data-release-binary="apple-darwin">
    <td class="release-arch desktop">Loading…</td>
    <td>💾 <a class="release-url">Loading…</a></td>
    <td><a class="release-sig">Loading…</a></td>
    <td><a class="release-checksum">Loading…</a></td>
  </tr>
</table>

## Download

You can download the appropriate tarball for your operating system with:

    curl -O -L https://files.radicle.xyz/releases/latest/radicle-$TARGET.tar.xz

Replace `$TARGET` with the appropriate target for your system:

<table>
  <thead>
    <th scope="col">OS</th>
    <th scope="col">Arch</th>
    <th scope="col">Target</th>
  </thead>
  <tr data-release-arch="x86_64" data-release-binary="unknown-linux-musl">
    <th scope="row" rowspan="2">Linux</th>
    <td>x86_64</td>
    <td>x86_64-unknown-linux-musl</td>
  </tr>
  <tr data-release-arch="aarch64" data-release-binary="unknown-linux-musl">
    <td>aarch64 (ARM)</td>
    <td>aarch64-unknown-linux-musl</td>
  </tr>
  <tr data-release-arch="x86_64" data-release-binary="apple-darwin">
    <th scope="row" rowspan="2">macOS</th>
    <td>x86_64</td>
    <td>x86_64-apple-darwin</td>
  </tr>
  <tr data-release-arch="aarch64" data-release-binary="apple-darwin">
    <td>aarch64 (ARM)</td>
    <td>aarch64-apple-darwin</td>
  </tr>
</table>

## Verification

Release tarballs are signed using the following SSH public key:

    ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIL460KIEccS4881p7PPpiiQBsxF+H5tgC6De6crw9rbU

The key's fingerprint is:

    SHA256:iTDjRHSIaoL8dpHbQ0mv+y0IQqPufGl2hQwk4TbXFlw

After you've downloaded the archive (`ARCHIVE.tar.xz` in the examples below),
verify the signature using the `.sig` file and `ssh-keygen`. The output must
match exactly, including the key fingerprint:

    $ ssh-keygen -Y check-novalidate -n file -s ARCHIVE.tar.xz.sig < ARCHIVE.tar.xz
    Good "file" signature with ED25519 key SHA256:iTDjRHSIaoL8dpHbQ0mv+y0IQqPufGl2hQwk4TbXFlw

To verify the checksum, download the `.sha256` file, and run:

    $ sha256sum -c ARCHIVE.tar.xz.sha256
    ARCHIVE.tar.xz: OK

The output must correspond to the above.

## Installation

To install, simply extract the files to a location in your `PATH`, for example:

    $ tar -xvJf ARCHIVE.tar.xz --strip-components=1 -C ~/.radicle/

This will place binaries in `~/.radicle/bin` and manuals in `~/.radicle/man`,
and overwrite any existing Radicle binaries.

<script>
  const releases = document.getElementById("releases");
  releases.classList.remove("hidden");

  fetch("https://files.radicle.xyz/releases/latest/radicle.json")
    .then(res => res.json())
    .then(data => {
      const version = data.version;
      const commit = data.commit;
      const release = document.getElementById("release");
      const releaseName = document.getElementById("release-name");
      const releaseInfo = document.getElementById("release-info");

      document.querySelectorAll("#releases [data-release-binary]").forEach((row) => {
        const arch = row.dataset.releaseArch;
        const binary = row.dataset.releaseBinary;
        const archive = `radicle-${version}-${arch}-${binary}.tar.xz`;
        const url = `https://files.radicle.xyz/releases/latest/${archive}`;

        row.querySelector(".release-arch").innerText = arch;
        row.querySelector(".release-url").innerText = archive;
        row.querySelector(".release-sig").innerText = ".sig";
        row.querySelector(".release-checksum").innerText = ".sha256";

        row.querySelector(".release-url").href = url;
        row.querySelector(".release-sig").href = `${url}.sig`;
        row.querySelector(".release-checksum").href = `${url}.sha256`;
      });

      release.classList.remove("loading");
      releaseName.innerText = `Radicle ${version}`;
      releaseInfo.innerHTML = `Built from commit <code>${commit}</code> on ${new Date(data.timestamp * 1000).toUTCString()}.`;
    });
</script>
