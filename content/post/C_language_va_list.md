+++
title = "C语言使用变长参数"
author = ["alilili"]
lastmod = 2023-09-18T00:27:15+08:00
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
