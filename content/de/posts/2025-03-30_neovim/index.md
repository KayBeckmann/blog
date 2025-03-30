---
title: "NeoVim"
date: "2025-03-30T00:00:00Z"
draft: false
description: "Konfiguration für mein NeoVim"
isStarred: false
layout: "monthly_overview"
---

# Wie du dein Vimwiki mit Git automatisch synchronisierst

## Einleitung

Vimwiki ist ein großartiges Tool für persönliche Notizen, Journaling und Wissensmanagement direkt im Neovim-Editor. Wenn du auf mehreren Geräten arbeitest oder einfach deine Daten sichern möchtest, bietet sich die Integration mit Git an. In diesem Beitrag zeige ich dir, wie du:

- Vimwiki im Markdown-Format einrichtest
- ein Git-Repository für dein Wiki nutzt
- automatische Synchronisation beim Speichern und Öffnen einrichtest
- ein Kommando `:Wsync` für manuelles Pushen hinzufügst

---

## Voraussetzungen

- **Neovim >= 0.8** (idealerweise 0.10+)
- Vimwiki installiert über `lazy.nvim` oder `vim-plug`
- Git ist installiert und initialisiert im Vimwiki-Ordner:
  ```bash
  cd ~/vimwiki
  git init
  git remote add origin git@github.com:DEINUSER/vimwiki.git
  ```

---

## 1. Vimwiki im Markdown-Modus konfigurieren

In deiner `init.lua` oder vor dem Laden des Plugins:

```lua
vim.g.vimwiki_list = {
  {
    path = '~/vimwiki/',
    syntax = 'markdown',
    ext = '.md',
    diary_rel_path = 'diary/',
    diary_index = 'journal-index',
    auto_diary_index = 1,
  }
}

vim.g.vimwiki_global_ext = 0
vim.g.vimwiki_markdown_link_ext = 1
vim.g.vimwiki_diary_template = '~/.vimwiki/diary_template.md'
vim.g.vimwiki_diary_header = ' '
```

---

## 2. Lua-Funktion zum Git-Sync

Speichere diese Datei als `~/.config/nvim/lua/plugins/config/sync.lua`:

```lua
-- plugins/config/sync.lua

local function wiki_git_sync()
  local wiki_path = vim.fn.expand("~/vimwiki")
  local check_changes = vim.fn.system("cd " .. wiki_path .. " && git status --porcelain")

  if check_changes ~= "" then
    local cmd = [[
      cd ]] .. wiki_path .. [[ && \
      git add . && \
      git commit -m "Autosync: ]] .. os.date("%Y-%m-%d %H:%M:%S") .. [[" >/dev/null 2>&1 && \
      git pull --rebase >/dev/null 2>&1 && \
      git push >/dev/null 2>&1
    ]]
    os.execute(cmd)
  end
end

-- :Wsync manuell aufrufbar
vim.api.nvim_create_user_command("Wsync", wiki_git_sync, {})

-- Beim Speichern automatisch synchronisieren
vim.api.nvim_create_autocmd("BufWritePost", {
  pattern = {"~/vimwiki/*.md", "~/vimwiki/**/*.md"},
  callback = wiki_git_sync,
})

-- Beim Öffnen automatisch Pull ausführen
vim.api.nvim_create_autocmd("BufEnter", {
  pattern = {"~/vimwiki/*.md", "~/vimwiki/**/*.md"},
  callback = function()
    os.execute("cd ~/vimwiki && git pull --rebase >/dev/null 2>&1 &")
  end,
})
```

---

## 3. Integration in `plugins/init.lua`

```lua
{ "vimwiki/vimwiki", config = function()
    require("plugins.config.vimwiki")
    require("plugins.config.sync")
  end },
```

---

## 4. Dein Workflow ab sofort

- ✍️ Schreibe Einträge wie gewohnt mit `\jj`, `\ji` etc.
- 💾 Beim Speichern wird automatisch synchronisiert
- 🧠 Beim Öffnen bekommst du die aktuellste Version per Pull
- 🤖 Mit `:Wsync` kannst du manuell synchronisieren

Optional kannst du mit `cron`, `rsync` oder Cloud-Lösungen eine zusätzliche Backup-Strategie kombinieren.

---

## Fazit

Mit dieser Konfiguration hast du ein **sicheres, portables und automatisiertes Journalsystem**, das sich ideal in deinen Coding-Workflow integriert. Du arbeitest vollständig offlinefähig und kannst jederzeit mit Git deine Inhalte zurückholen, teilen oder auf mehreren Geräten nutzen.

Wenn du noch Features wie `:GenerateWeek`, mobile Integration oder Obsidian-Kompatibilität willst – lass es mich wissen 😎

## Kaffee

Über einen  
[Kaffee](https://www.buymeacoffee.com/snuppedelua)  
würde ich mich auf jeden Fall freuen.
