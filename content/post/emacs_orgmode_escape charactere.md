+++
title = "emacs org-mode 设置转义字符"
author = ["alilili"]
lastmod = 2023-08-25T23:20:45+08:00
draft = false
+++

{{< highlight lisp >}}
    #+OPTIONS: ^:{}
    (setq org-export-with-sub-superscripts nil)
    :sub-superscript nil
    :sub-superscript {}
    (setq org-export-with-sub-superscripts '{})
{{< /highlight >}}
