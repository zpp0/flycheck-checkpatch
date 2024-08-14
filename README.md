# flycheck-checkpatch

An easy way to write Linux kernel or QEMU code
according to the style guidelines.

## Usage
The package is available from MELPA, install with the following:

```lisp
(use-package flycheck-checkpatch
  :config
  (flycheck-checkpatch-setup))
```

When enabled, flycheck highlights style issues
reported by the checkpatch.pl script.

![flycheck-checkpatch example](screenshot.png)

## Chain multiple checkers

Here is a configuration example of running checkpatch after rtags:

```lisp
(use-package flycheck-checkpatch
  :after flycheck-rtags
  :init
  (defun my-flycheck-checkpatch-setup ()
    (flycheck-add-next-checker 'rtags 'checkpatch-code))
  :config
  (flycheck-checkpatch-setup)
  (add-hook 'c-mode-hook #'my-flycheck-checkpatch-setup))
```

LSP mode requires more effort to chain the checkers, take a look at [this comment](https://github.com/flycheck/flycheck/issues/1762#issuecomment-750458442).
Here is an example:

```lisp
(use-package flycheck-checkpatch
  :after lsp
  :hook
  (lsp-managed-mode . (lambda ()
                        (when (derived-mode-p 'c-ts-mode)
                          (setq my/flycheck-local-cache '((lsp . ((next-checkers . (checkpatch-code)))))))))
  :config
  (defvar-local my/flycheck-local-cache nil)

  (defun my/flycheck-checker-get (fn checker property)
    (or (alist-get property (alist-get checker my/flycheck-local-cache))
        (funcall fn checker property)))

  (advice-add 'flycheck-checker-get :around 'my/flycheck-checker-get)

  (flycheck-checkpatch-setup))
```
