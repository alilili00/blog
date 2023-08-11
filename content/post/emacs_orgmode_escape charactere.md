+++
title = "emacs org-mode 设置转义字符"
author = ["alilili"]
lastmod = 2023-09-18T00:27:17+08:00
draft = false
+++

{{< highlight lisp >}}
    #+OPTIONS: ^:{}
    (setq org-export-with-sub-superscripts nil)
    :sub-superscript nil
    :sub-superscript {}
    (setq org-export-with-sub-superscripts '{})
{{< /highlight >}}
