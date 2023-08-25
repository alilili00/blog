+++
title = "C语言使用变长参数"
author = ["alilili"]
lastmod = 2023-08-25T22:06:47+08:00
draft = false
+++

{{< highlight C >}}
void
die(const char *errstr, ...)
{
	va_list ap;

	va_start(ap, errstr);
	vfprintf(stderr, errstr, ap);
	va_end(ap);
	exit(1);
}
{{< /highlight >}}
