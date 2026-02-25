# Laptop setup

Here is how I like to setup my laptop.

Design goals:

- **Simple**. Simpler is better. Easier to remember where things are and how to do tasks. Fewer things to break.
- **Minimal**. By carrying fewer things, it's easier to keep things tidy and running efficiently. Delete or disable unnecessary stuff. Less to manage and upgrade.
- **Offload important stuff to the cloud**. That way I can access it from multiple devices as needed. And migrating to a new laptop is easy as there is less to backup/restore.
- **Distraction free**. Disable notifications and anything that will interrupt me so that I can be more focused when I'm working on a project.
- **Snappy and efficient**. Favor tools that are fast and efficient over those that are slow and bloated. There's something satisfying about a snappy experience, and it helps to stay in the flow of your tasks better when context switching is instantaneous.

## Hardware

- MacBook Air for portability
- Neoprene case (Incase or similar brand) to keep it safe for transport
- No stickers. I like it clean and stock.
- Accessories: extra power cable, USB C dongle with 2 HDMI outputs
- 2 external monitors (same brand, Sceptre 27" curved)
- Vivo dual monitor stand
- Beats headphones
- Yeti tumbler for water
- Coffee or tea

## Software

- Chrome as primary web browser
    - Bookmarks: Gmail, Google Calendar and Tasks.
    - Plugins: 1password
    - Enable Developer Tools
    - Disable Password auto-fill (use 1password for this)
- [1password](https://1password.com/downloads/mac) app for managing passwords, passkeys and secrets across devices
- [Stats](https://mac-stats.com) app for system stats. CPU %, RAM %, Battery showing time to discharge and %.
- [Rectangle](https://rectangleapp.com) app for keyboard shortcuts to move and resize windows. 
    - CTRL ˄ OPTION ⌥ RETURN ⏎ to maximize window
    - CTRL ˄ OPTION ⌥ LEFT ARROW ← to move window to left half of screen
    - CTRL ˄ OPTION ⌥ RIGHT ARROW → to move window to right half of screen
- [Obsidian](https://obsidian.md) for organizing my notes using Markdown in folders:
    - `Goals\` with a page for each year's goals. Each page is broken down by quarter and month with a simple `- [ ]` todo list to track accomplishment.
    - `Templates\` with a template for weekly plans with sections for `Goals` (work and personal), `Review` (accomplishments, things I learned), `Retrospective` (what's going well, things to improve), and `Notes`.
    - `Weekly Plans\` with a page for each week numbered `YYYY-MM-DD Weekly Plan`. 
    - `Projects\` with a page for each project.  A catch all `Ideas` page with bulleted list of potential project ideas.
- [Syncthing](https://syncthing.net) to sync `~/Sync/` folder between laptops locally. This is how I sync Obsidian workspaces with notes.

### Developer tools

- [Homebrew](https://brew.sh) for installing software
- Shell: Zsh
- Terminal: [iTerm2](https://iterm2.com)
    - Configure iTerm2 → Preferences → Profiles → Text and set Font to `MesloLGS NF` after installing p10k prompt
- Nerd Font for terminal: `brew install --cask font-meslo-lg-nerd-font`
- Prompt: [powerlevel10k](https://github.com/romkatv/powerlevel10k): `brew install powerlevel10k`
    - Configure [Nerd Font](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#fonts) automatically for `MesloLGS NF`.
    - Enable Instant Prompt
- Git: `brew install git`
- Configure Git (see `.gitconfig` section below)
- [Setup SSH key for Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) and add to ssh-agent
- [Golang](https://go.dev): `brew install go`
- [uv](https://docs.astral.sh/uv/) for python virtualenv management: `brew install uv`
- [Claude Code](https://code.claude.com/docs/en/overview) for AI coding assistance from the terminal.
- [Claude Desktop](https://claude.ai/download) for chat - research, Q&A, collaborative document writing
- [Cursor](https://cursor.com/home) as AI IDE, alternative to VS Code.
    - Configuration:
        - Cursor Settings > Models > Anthropic API key
        - VS Code Settings > Terminal Font set as `MesloLGS NF`
        - VS Code Settings > Theme > Cursor Dark
    - Plugins:
        - Github Pull Requests
        - GitLens
        - Go
        - Jupyter
        - Python
        - Python Debugger
        - Makefile Tools
        - YAML
        - Docker
- [Sublime Text](https://www.sublimetext.com) as a very fast text editor. Mostly for basic text editing of YAML and config, not programming.
- [Xcode](https://developer.apple.com/xcode/)
- [Docker Desktop](https://docs.docker.com/desktop/setup/install/mac-install/)
    - Configuration:
        - Disable auto-start on login I can launch only when needed
        - Enable Kubernetes
- [Postman](https://www.postman.com/) for API testing
- [FreeLens](https://github.com/freelensapp/freelens) for k8s management `brew install --cask freelens`
- [pgAdmin](https://www.pgadmin.org/download/) for Postgresql GUI
- [Mongo Atlas](https://www.mongodb.com/try/download/compass)
- [DBeaver](https://dbeaver.io) for database GUI: `brew install --cask dbeaver-community`
- [Figma Desktop](https://www.figma.com/downloads/) for UI mockups

### Tips

**Keeping things tidy**

- Never store things on Desktop
- Delete unused Applications
- Downloads - either organize it somewhere long term (Documents or Projects folder, Google Drive) or delete it
- Empty Trash every day


**Customized prompt**

![prompt](./../assets/img/p10k_prompt.png)

**.gitconfig**

```
[user]
        name = Dustin Burke
        email = <EMAIL>

[alias]
        up = !git pull --rebase --prune $@ && git submodule update --init --recursive
        co = checkout
        cob = checkout -b
        cm = !git add -A && git commit -m
        save = !git add -A && git commit -m 'SAVEPOINT'
        wip = commit -am WIP
        undo = reset HEAD~1 --mixed
        amend = commit -a --amend
        wipe = !git add -A && git commit -qm 'WIPE SAVEPOINT' && git reset HEAD~1 --hard
        bclean = !f() { git checkout ${1-master} && git branch --merged ${1-master} | grep -v  ${1-master}$ | xargs git branch -d; }; f
        bdone = !f() { git checkout ${1-master} && git up && git bclean ${1-master}; }; f
        migrate = !f(){ CURRENT=$(git symbolic-ref --short HEAD); git checkout -b $1 && git branch --force $CURRENT ${3-$CURRENT@{u}} && git rebase --onto ${2-master} $CURRENT; }; f
        lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
        please = push --force-with-lease
        commend = commit --amend --no-edit
        st = status -sb -uall
        grog = log --graph --abbrev-commit --decorate --all --format=format:"%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(dim white) - %an%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n %C(white)%s%C(reset)"
        bonk = push --set-upstream origin HEAD
        #trim = !f() { git branch | grep -v '^\*' | xargs git branch -d; }; f
        #squash = !f() { git reset $(git merge-base ${1-master} $(git rev-parse --abbrev-ref HEAD)); }; f
        branchdate = for-each-ref --count=10 --sort=-committerdate refs/heads/ --format=%(authordate:short)%09%(objectname:short)%09%1B[0;33m%(refname:short)%1B[m%09

[pull]
        rebase=true
        default=current
        autosetupremote=true

[filter "lfs"]
        clean = git-lfs clean -- %f
        smudge = git-lfs smudge -- %f
        process = git-lfs filter-process
        required = true

[credential]
        helper = store --file ~/git.credentials

[init]
        defaultbranch = main
```

**Fast terminal startup**

Silence the "last logged in" message when opening new terminal:

```sh
touch .hushlogin
```

Edit `~/.zshrc` to comment out everything except what's critical.

When installing powerlevel10k, enable "instant prompt".

**Use Touch ID for sudo in terminal**

Copy the template:

```bash
sudo cp /etc/pam.d/sudo_local.template /etc/pam.d/sudo_local
```

And edit `/etc/pam.d/sudo_local` to uncomment the `pam_tid.so` line so that the Pluggable Auth Module (PAM) for Touch ID (tid) is enabled:

```
# sudo_local: local config file which survives system update and is included for sudo
# uncomment following line to enable Touch ID for sudo
auth       sufficient     pam_tid.so
```

**Vim plugin and color scheme**

Install [vim-plug](https://github.com/junegunn/vim-plug)

```sh
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Edit `~/.vimrc`:
```
if !has("gui_running")
    set background=dark
endif
set autoindent
set expandtab
set shiftwidth=8
set showmatch
set smarttab
set tabstop=8
syntax on
filetype on
set hlsearch
set updatetime=1000

call plug#begin()

" List your plugins here
Plug 'tpope/vim-sensible'
Plug 'morhetz/gruvbox'

call plug#end()
```

Then run `:PlugInstall` command in vim.

Add `autocmd vimenter * ++nested colorscheme gruvbox` line to end of `.vimrc` and restart.


**Organizing code**

```text
~/Projects
├── learning                    - github repository
└── some-app-backend-go         - github repository with a descriptive project name, a hint about the type of project (backend, cli, etc.) and which language or framework as applicable.
```

**Mac Settings**

- Appearance: Auto (light and dark mode)
- Dock hiding and magnification on
- Spotlight search for Applications, Developer, Documents, Folders and System Settings only.  Less to index, faster performance, better results.
- Disable notifications for most things.
- Location services off

### Agentic coding

Copy [AGENTS.md](AGENTS.md) to `~/.claude/CLAUDE.md`.

`~/.claude/settings.json`:
```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "WebSearch",
      "WebFetch",

      "Read",
      "Write",
      "Edit",
      "MultiEdit",
      "Glob",
      "Grep",
      "LS",

      "Bash(python *)",
      "Bash(python3 *)",
      "Bash(pip *)",
      "Bash(pip3 *)",
      "Bash(uv *)",
      "Bash(poetry *)",
      "Bash(pytest *)",
      "Bash(ruff *)",
      "Bash(black *)",
      "Bash(mypy *)",
      "Bash(flake8 *)",
      "Bash(isort *)",
      "Bash(pre-commit *)",

      "Bash(git status)",
      "Bash(git log *)",
      "Bash(git diff *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(git checkout *)",
      "Bash(git branch *)",
      "Bash(git stash *)",
      "Bash(git pull *)",
      "Bash(git fetch *)",
      "Bash(git show *)",

      "Bash(cat *)",
      "Bash(ls *)",
      "Bash(find *)",
      "Bash(grep *)",
      "Bash(echo *)",
      "Bash(pwd)",
      "Bash(which *)",
      "Bash(env)",
      "Bash(printenv *)",
      "Bash(jq *)",

      "Bash(docker ps *)",
      "Bash(docker logs *)",
      "Bash(docker build *)",
      "Bash(docker-compose up *)",
      "Bash(docker-compose down *)",

      "Bash(make *)"
    ],
    "deny": [
      "Read(**/.env)",
      "Read(**/.env.*)",
      "Read(**/secrets/**)",
      "Read(**/*.pem)",
      "Read(**/*.key)",

      "Bash(rm *)",
      "Bash(rmdir *)",
      "Bash(sudo *)",
      "Bash(su *)",
      "Bash(curl *)",
      "Bash(wget *)",
      "Bash(ssh *)",
      "Bash(scp *)",
      "Bash(git push *)",
      "Bash(git rebase *)",
      "Bash(git reset --hard *)",
      "Bash(git clean *)",
      "Bash(chmod *)",
      "Bash(chown *)",
      "Bash(kill *)",
      "Bash(pkill *)"
    ]
  },
  "env": {
    "BASH_DEFAULT_TIMEOUT_MS": "30000"
  }
}
```

