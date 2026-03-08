# Obsidian CLI — Full Command Reference

This is the complete command reference. The main SKILL.md covers the most common commands. Consult this file for less frequently used operations.

## File and vault info

```bash
Obsidian file file="My Note"                    # file metadata
Obsidian files folder=sources                   # list files in a folder
Obsidian files ext=base                         # filter by extension
Obsidian files total                            # file count
Obsidian folders                                # list all folders
Obsidian folder path=sources info=files         # folder info
Obsidian vault                                  # vault overview (name, path, size)
Obsidian vaults verbose                         # all known vaults with paths
Obsidian wordcount file="My Note"               # word and character count
Obsidian recents                                # recently opened files
Obsidian random                                 # open a random note
Obsidian random:read                            # read a random note
```

## Aliases

```bash
Obsidian aliases verbose                        # all aliases with file paths
Obsidian aliases file="My Note"                 # aliases for a specific file
```

## Bookmarks

```bash
Obsidian bookmarks                              # list bookmarks
Obsidian bookmarks format=json verbose          # detailed bookmark list
Obsidian bookmark file="My Note"                # bookmark a file
Obsidian bookmark file="My Note" subpath="#Section"  # bookmark a heading
Obsidian bookmark url="https://example.com" title="Example"
Obsidian bookmark search="AI agents"            # bookmark a search query
```

## History and versioning

```bash
Obsidian history:list                           # files with history
Obsidian history file="My Note"                 # version history for a file
Obsidian history:read file="My Note" version=1  # read a historical version
Obsidian history:restore file="My Note" version=3  # restore a version
```

## Sync

```bash
Obsidian sync:status                            # current sync status
Obsidian sync off                               # pause sync
Obsidian sync on                                # resume sync
Obsidian sync:deleted                           # files deleted in sync
Obsidian sync:history file="My Note"            # sync version history
Obsidian sync:read file="My Note" version=2     # read a sync version
Obsidian sync:restore file="My Note" version=2  # restore a sync version
```

## Diff (local and sync versions)

```bash
Obsidian diff file="My Note"                    # list versions
Obsidian diff file="My Note" from=1 to=3        # diff between versions
Obsidian diff file="My Note" filter=sync        # sync versions only
```

## Plugin management

```bash
Obsidian plugins                                # all installed plugins
Obsidian plugins:enabled                        # enabled plugins only
Obsidian plugins filter=community versions      # community plugins with versions
Obsidian plugin id=dataview                     # info about a specific plugin
Obsidian plugin:enable id=dataview              # enable a plugin
Obsidian plugin:disable id=dataview             # disable a plugin
Obsidian plugin:install id=some-plugin enable   # install and enable
Obsidian plugin:uninstall id=some-plugin        # uninstall
Obsidian plugin:reload id=my-plugin             # reload (for development)
Obsidian plugins:restrict on                    # enable restricted mode
Obsidian plugins:restrict off                   # disable restricted mode
```

## Themes and snippets

```bash
Obsidian theme                                  # active theme
Obsidian themes versions                        # installed themes with versions
Obsidian theme:set name="Minimal"               # set active theme
Obsidian theme:install name="Minimal" enable    # install and activate
Obsidian theme:uninstall name="Minimal"         # uninstall
Obsidian snippets                               # list CSS snippets
Obsidian snippets:enabled                       # enabled snippets
Obsidian snippet:enable name="my-snippet"       # enable a snippet
Obsidian snippet:disable name="my-snippet"      # disable a snippet
```

## Hotkeys

```bash
Obsidian hotkeys                                # all hotkeys
Obsidian hotkeys verbose                        # show if custom or default
Obsidian hotkey id="editor:toggle-bold"         # hotkey for a specific command
```

## Obsidian commands

Any command from the Obsidian command palette can be executed.

```bash
Obsidian commands                               # list all command IDs
Obsidian commands filter=editor                 # filter by prefix
Obsidian command id="editor:toggle-bold"        # execute a command
```

## Tabs and workspace

```bash
Obsidian tabs                                   # list open tabs
Obsidian tabs ids                               # include tab IDs
Obsidian tab:open file="My Note"                # open file in new tab
Obsidian workspace                              # show workspace tree
Obsidian workspace ids                          # include workspace item IDs
```

## Unique notes

```bash
Obsidian unique name="Zettelkasten Note" content="Content" open
```

## Vault reload and restart

```bash
Obsidian reload                                 # reload the vault
Obsidian restart                                # restart the app
Obsidian version                                # Obsidian version
```

## Developer tools

```bash
Obsidian eval code="app.vault.getFiles().length"   # run JavaScript
Obsidian devtools                                   # toggle dev tools
Obsidian dev:dom selector=".workspace" text         # query DOM
Obsidian dev:css selector=".nav-file-title"         # inspect CSS
Obsidian dev:console                                # show console messages
Obsidian dev:errors                                 # show errors
Obsidian dev:screenshot path="screenshot.png"       # take a screenshot
Obsidian dev:mobile on                              # enable mobile emulation
Obsidian dev:cdp method="Page.reload"               # Chrome DevTools Protocol
```
