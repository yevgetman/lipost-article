# lipost-article

> **⚠️ DEPRECATED — merged into [yevgetman/lipost](https://github.com/yevgetman/lipost).**
>
> This standalone tool is no longer maintained. All functionality is now part of the unified `lipost` suite as the `article` subcommand.
>
> **Migration:**
>
> ```bash
> # Install the unified suite
> git clone https://github.com/yevgetman/lipost.git
> cd lipost && ./install.sh
> lipost init      # migrates ~/.config/linkedin-cli/ → ~/.config/lipost/
>
> # New invocation
> lipost article <url>                  # was: lipost-article <url>
> lipost article <url> --queue          # was: lipost-article <url>  (then [q]ueue)
> lipost article <url> --queue-pending  # was: lipost-article --queue-pending <url>
> ```
>
> The `lipost-article` binary stops being installed once you symlink the unified `lipost` over the top. After verifying the new flow works, you can remove this repo.
>
> ---
>
> _The original README follows for historical reference._
>
> ---

Turn an article URL into a LinkedIn post.

A small CLI that:

1. Takes an article URL (prompted, or passed as an arg).
2. Invokes Claude Code (`claude -p`, Opus 4.7, `--dangerously-skip-permissions`) to fetch and react to the article.
3. Wraps your own voice prompt (`prompt.md`) and formatting style guide (`style.md`) around the article.
4. Shows the draft for review — approve, edit (`$EDITOR`), regenerate (with an optional steering note), or cancel.
5. On approval, pipes the caption into the [`lipost`](https://github.com/yevgetman/lipost) CLI to publish.

Text posts only for now. Image generation is intentionally not wired up.

## Requirements

- `claude` (Claude Code CLI) on PATH, logged in, with access to Opus 4.7.
- `lipost` on PATH, with `lipost init` + `lipost auth` already run.
- A `prompt.md` (your persona/voice) and `style.md` (formatting rules). See below.

## Setup

Drop two files alongside the script — both are gitignored, so your private voice never lands in git:

```
lipost-article/
  lipost-article
  prompt.md       # your voice prompt — persona, audience, banned phrases, length
  style.md        # formatting layer — punctuation, capitalization, paragraph rules
```

Either create these by hand or symlink existing files:

```bash
ln -s ~/path/to/your/prompt.md /path/to/lipost-article/prompt.md
ln -s ~/path/to/your/style.md  /path/to/lipost-article/style.md
```

You can also point the lookup elsewhere with env vars (each is independent):

```bash
export LIPOST_ARTICLE_PROMPT=~/voice/linkedin-prompt.md
export LIPOST_ARTICLE_STYLE=~/voice/style.md
```

### What goes in `prompt.md`

Your persona/voice/audience rules — who is writing, what they sound like, who they're writing for, banned phrases, length budget, anything else stylistic that's about *what to say*. The script appends a fixed wrapper around it that handles the article-specific instructions (fetch the URL, react to one specific thing, end with the URL line, etc.) and the JSON output schema, so `prompt.md` itself only needs voice rules — not output format or article-handling logic.

### What goes in `style.md`

Mechanical formatting rules: punctuation, capitalization, paragraph breaks, character set, emoji policy, length caps. Stuff that describes *how* the text looks, not *what* it says.

## Usage

```bash
lipost-article                                    # prompts for URL
lipost-article https://blog.example.com/post      # generate + review + post
lipost-article --dry-run https://...              # everything except the actual publish
```

Review actions: `[A]pprove`, `[e]dit`, `[r]egenerate`, `[c]ancel`. Regenerate accepts an optional steering note that's fed back to Claude alongside the previous draft.

## Install on PATH

```bash
chmod +x lipost-article
ln -s "$PWD/lipost-article" ~/.local/bin/lipost-article  # or any dir on your PATH
```

## License

MIT — see [LICENSE](LICENSE).
