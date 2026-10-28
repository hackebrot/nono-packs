# claude-hardened

Hardened nono profile for Claude Code, based on the `claude` profile from
[nolabs-ai/nono-packs].

## Differences from nolabs-ai/claude

- No grant or `bypass_protection` for `~/Library/Keychains`, so nono's
  required `deny_keychains_macos` group applies.
- The `claude_code_macos` group is replaced by its two non-keychain read
  paths: `~/.local/share/claude` and the Claude Code URL Handler app.
- The `user_caches_macos` group is replaced by Claude Code's own cache
  directory, to guard against cache poisoning: the sandbox can't write to
  caches that other apps on the host read and trust. `bin/ensure-dirs.sh`
  creates the directory if it's missing; the grant applies from the next
  session.
- The `vscode_macos` and `vscode_linux` groups are removed: VS Code runs its
  extensions and settings on the host, so the sandbox can't change them.
  Claude Code's VS Code integration isn't supported.
- Only an allow-list of environment variables reaches the sandbox: what
  Claude Code needs for paths, locale and the terminal. Anything else exported
  in your shell, like tokens, stays out.
- Claude Code's login runs through nono's [sandboxed OAuth login].
- Network access is limited to the `claude-code` network profile plus
  `downloads.claude.ai`, since OAuth capture turns on host filtering.

Without keychain access, Claude Code stores its login in
`~/.claude/.credentials.json`. OAuth capture replaces the tokens from login
and refresh with placeholders, and nono adds the real ones to requests to
Anthropic. It doesn't change a file that already holds a real token: if one
exists from a login without capture, run `/logout` first. Then run `/login`
inside the sandbox. Profiles without OAuth capture, including
`nolabs-ai/claude`, can't use that login; if you log in with one of them,
`/logout` again before returning to this profile.

## Usage

Requires nono 0.80.0 or later. The nono plugin for Claude Code comes from the
upstream pack, so install both:

```sh
nono pull nolabs-ai/claude
nono pull hackebrot/claude-hardened
nono run --profile hackebrot/claude-hardened --allow-cwd -- claude
```

## License

`policy.json` and `bin/ensure-dirs.sh` are from [nolabs-ai/nono-packs]
(Apache-2.0) and modified as described above; the OAuth capture configuration
in `policy.json` is adapted from nono's documentation (Apache-2.0).

[nolabs-ai/nono-packs]: https://github.com/nolabs-ai/nono-packs/tree/0735dcd67de4d87223e76095e07ffd5b43e9149a/claude
[sandboxed OAuth login]: https://nono.sh/docs/cli/features/sandboxed-oauth-logins
