emacs-lsp
=========

[![Join the chat at https://gitter.im/emacs-lsp/lsp-mode](https://badges.gitter.im/emacs-lsp/lsp-mode.svg)](https://gitter.im/emacs-lsp/lsp-mode?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Build Status](https://travis-ci.org/emacs-lsp/lsp-mode.svg?branch=master)](https://travis-ci.org/emacs-lsp/lsp-mode)
[![MELPA Stable](https://stable.melpa.org/packages/lsp-mode-badge.svg)](https://stable.melpa.org/#/lsp-mode)
[![MELPA](http://melpa.org/packages/lsp-mode-badge.svg)](http://melpa.org/#/lsp-mode)

A Emacs Lisp library for implementing clients for servers using Microsoft's
[Language Server Protocol](https://github.com/Microsoft/language-server-protocol/) (v3.0).

The library is designed to integrate with existing Emacs IDE frameworks
(completion-at-point, xref (beginning with Emacs 25.1), flycheck, etc).

_Note: Starting from version 4.0, flycheck support has been moved to the package [lsp-ui](https://github.com/emacs-lsp/lsp-ui)_

_Note: Refer to [lsp-mode NEXT](./README-NEXT.md) for `lsp-mode` single entry point.

## Installation

Clone this repository to a suitable path, and add

```emacs-lisp
(add-to-list 'load-path "<path to emacs-lsp>")
(require 'lsp-mode)

(lsp-define-stdio-client
 ;; This can be a symbol of your choosing. It will be used as a the
 ;; prefix for a dynamically generated function "-enable"; in this
 ;; case: lsp-prog-major-mode-enable
 lsp-prog-major-mode
 "language-id"
 ;; This will be used to report a project's root directory to the LSP
 ;; server.
 (lambda () default-directory)
 ;; This is the command to start the LSP server. It may either be a
 ;; string containing the path of the command, or a list wherein the
 ;; car is a string containing the path of the command, and the cdr
 ;; are arguments to that command.
 '("/my/lsp/server" "and" "args"))

;; Here we'll add the function that was dynamically generated by the
;; call to lsp-define-stdio-client to the major-mode hook of the
;; language we want to run it under.
;;
;; This function will turn lsp-mode on and call the command given to
;; start the LSP server.
(add-hook 'prog-major-mode #'lsp-prog-major-mode-enable)
```
to your .emacs, where `prog-major-mode` is the hook variable for a supported
programming language major mode.

## Adding support for languages
See [API docs](./doc/API.org)

## Examples

### completion
Completion is provided with the native `completion-at-point` (<kbd>C</kbd>-<kbd>M</kbd>-<kbd>i</kbd>),
 and should therefore work with any other completion backend. Async completion is provided by
 [company-lsp](https://github.com/tigersoldier/company-lsp).

![completion](./examples/completion.png)

### `eldoc` (Help on hover)
Hover support is provided with `eldoc`, which should be enabled automatically.

![eldoc](./examples/eldoc.png)

### Goto definition
Use <kbd>M</kbd> - <kbd>.</kbd> (`xref-find-definition`)
to find the definition for the symbol under point.

![gotodef](./examples/goto-def.gif)

### Symbol references
Use <kbd>M</kbd> - <kbd>?</kbd> (`xref-find-references`)
to find the references to the symbol under point.

![ref](./examples/references.png)

### Symbol Highlighting
![sym_highlight](./examples/sym_highlight.gif)

### Imenu
Add
```emacs-lisp
(require 'lsp-imenu)
(add-hook 'lsp-after-open-hook 'lsp-enable-imenu)
```
to your init file to enable imenu integration.

![imenu-1](./examples/imenu-1.png)
![imenu-2](./examples/imenu-2.png)

#### With [helm-imenu](https://github.com/emacs-helm/helm)
![helm-imenu](./examples/helm-imenu.gif)


### Rename
Use <kbd>M</kbd> - <kbd>x</kbd> `lsp-rename`.

![rename](./examples/rename.gif)

## Finer Control of Starting lsp-mode

In order to more finely control the `lsp-mode` startup, there are a number of
customizable variables.

`lsp-project-whitelist` : Defaults to `nil`.
`lsp-project-blacklist` : Defaults to `nil`.

`lsp-mode` will only be started if the given project root matches one pattern
in the whitelist, or does not match any pattern in the blacklist.

There are also the functions `lsp-MAJOR-MODE-whitelist-add` and
`lsp-MAJOR-MODE-whitelist-remove` to adjust the current buffer project root
entry on the whitelist.


### Hooks

`lsp-mode` provides a handful of hooks that can be used to extend and configure
the behaviour of language servers. A full list of hooks is available in the
[API documentation](./doc/API.org).

For example, you can automatically set [`projectile-project-root`](https://github.com/bbatsov/projectile/blob/master/doc/configuration.md#file-local-project-root-definitions) by attaching
the following function to `lsp-before-open-hook`:

```emacs-lisp
(defun my-set-projectile-root ()
  (when lsp--cur-workspace
    (setq projectile-project-root (lsp--workspace-root lsp--cur-workspace))))
(add-hook 'lsp-before-open-hook #'my-set-projectile-root)
```

### See also

[`eglot`](https://github.com/joaotavora/eglot) - An alternative and lighter LSP implementation.
