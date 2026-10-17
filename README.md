# mikan community addons

> [!CAUTION]
> **These addons are not mikan's.** They are written and published by third-party authors.
> The mikan project **does not review their code, does not build their images, does not
> support them and is not responsible for anything they do**: leaked bot tokens, stolen
> subscriber data, lost money, a broken panel. An addon here may be a copy of someone
> else's work, may change hands, and may turn malicious with its next release.
>
> Being listed here means only that the author's repository has the `mikan-addon` topic and
> its release passed format checks. **Merging a listing is not a review of the code.**
> Install a community addon only if you trust its author as much as you trust yourself with
> root on your server. Official addons are only the ones in
> [getmikan/marketplace](https://github.com/getmikan/marketplace).

The panel reads this registry's signed `index.json` (Addons → Community) and shows every
addon from it with a warning; each install and **each update** asks the admin to confirm
the risk again. Nothing here is fetched from the authors' hosts by the panels: a mikan
workflow reads the authors' releases and keeps a snapshot of each addon (version, image
digest, permissions) in `addons/`.

## How an addon gets here

1. Publish your addon as a protocol 2 addon
   ([PROTOCOL-v2.md](https://github.com/getmikan/marketplace/blob/main/PROTOCOL-v2.md)) with
   its image on a public registry, pinned by digest.
2. Attach `mikan-addon.json` to a GitHub release of your repository: one entry, or
   `{"title", "key", "adapters": [...]}` for several. Sign it (`mikan-addon.json.sig`,
   base64 Ed25519 over the file's bytes) and name your public key in `key`: a signed addon
   cannot be taken over by whoever gets your repository later.
3. Add the topic **`mikan-addon`** to the repository.

Every hour the indexer searches for the topic. A new repository gets a pull request here;
once a maintainer merges it, the addon shows in every panel. After that, new releases go in
by themselves, except when they need a person again:

| Change in your release | What happens |
|---|---|
| a new build (digest), same key, same permissions or narrower | goes in within the hour |
| another signing key, signed now when it was not, or no longer signed | pull request |
| the repository renamed or moved to another owner | pull request |
| a new permission `users:all` or `users:grant` | pull request |
| a new addon id | pull request |
| an addon no longer in the release | removed within the hour |
| the repository archived, made private, deleted, or given the topic `noindex` | all its addons removed within the hour |

An id belongs to the first repository merged with it; the ids of the official catalog and
`telegram` are never a community addon's. Payment adapters are not accepted here: money
goes only through the official ones.

## Revoked addons

`revoked.json` lists the addons mikan took out by hand (stolen code, malware, a broken
promise), by `id` or by `repo`, with a `reason` and a date. A panel that has one installed
shows the admin a red warning with the reason and refuses to install or update it.

Report an addon: open an issue here with the repository and what is wrong.

## Layout

```
addons/<owner>/<id>.json   a snapshot, written by the indexer; edit only through a pull request
revoked.json               the revoked addons, edited by hand
```

The indexer is `cmd/community` in [getmikan/marketplace](https://github.com/getmikan/marketplace).
Check the published index by hand:

```sh
curl -fsSLO https://github.com/getmikan/marketplace-community/releases/latest/download/index.json
curl -fsSLO https://github.com/getmikan/marketplace-community/releases/latest/download/index.json.sig
go run github.com/getmikan/marketplace/cmd/community@latest verify index.json
```

## License and attribution

The original documentation, automation, and other original content in this repository are
licensed under the [GNU Affero General Public License v3.0 only](LICENSE)
(`AGPL-3.0-only`), to the extent the respective contributors hold the necessary rights.

Copyright (c) 2026 Miroshka and contributors.

Original repository: https://github.com/getmikan/marketplace-community

Redistributors of covered material must comply with the AGPLv3, including preserving
applicable copyright and license notices, identifying modifications, and making
corresponding source available when required. The license does **not** prohibit forks.

The registry references third-party addons, images, repositories and descriptions.
Their respective authors retain their rights. This repository's license does not
relicense any linked third-party software or content that its contributors do not own.
