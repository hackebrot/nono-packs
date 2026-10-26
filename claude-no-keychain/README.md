# claude-no-keychain

nono profile for Claude Code without macOS keychain access, based on the
`claude` profile from [nolabs-ai/nono-packs].

## Differences from nolabs-ai/claude

- No grant or `bypass_protection` for `~/Library/Keychains`, so nono's
  required `deny_keychains_macos` group applies.
- The `claude_code_macos` group is replaced by its two non-keychain read
  paths: `~/.local/share/claude` and the Claude Code URL Handler app.

Claude Code stores its login in `~/.claude/.credentials.json` instead. Run
`/login` inside the sandbox on first use.

## Usage

The nono plugin for Claude Code comes from the upstream pack, so install both:

```sh
nono pull nolabs-ai/claude
nono pull hackebrot/claude-no-keychain
nono run --profile hackebrot/claude-no-keychain --allow-cwd -- claude
```

## License

`policy.json` and `bin/ensure-dirs.sh` are from [nolabs-ai/nono-packs]
(Apache-2.0). `policy.json` is modified as described above.

[nolabs-ai/nono-packs]: https://github.com/nolabs-ai/nono-packs/tree/0735dcd67de4d87223e76095e07ffd5b43e9149a/claude

