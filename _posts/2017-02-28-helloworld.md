---
layout: post
title:  "Witaj, świecie!"
date:   2017-02-28 13:17:38 +0100
categories: generic
---
Wpis testowy, przewijaj dalej.

{% highlight c %}
int TRAFFIC_LIGHT1_PORT = 255;

void traffic_light1_green()
{
  if(TRAFFIC_LIGHT1_PORT == 255) { return; }
  digitalWrite(TRAFFIC_LIGHT1_PORT * 3 + 2, LOW);
  digitalWrite(TRAFFIC_LIGHT1_PORT * 3 + 3, LOW);
  digitalWrite(TRAFFIC_LIGHT1_PORT * 3 + 4, HIGH);
}
{% endhighlight %}