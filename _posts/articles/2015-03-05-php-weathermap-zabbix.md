---
layout: post
title: Integrating PHP Weathermap and Zabbix
excerpt: ""
categories: articles
tags: [zabbix, monitoring, weathermap]
comments: true
share: true
---

### Maps in Zabbix

Zabbix provides a network map tool, well described in [The Hidden Power of Network Maps on Zabbix](http://www.slideshare.net/xsbr/rihards-olups-thehiddenpowerofnetworkmaps) (by Ricardo Santos).
Its main advantage is the integration of potentially all the monitoring data, besides network utilization.
It is yet quite limited for now and cannot represent graphically network bandwidth utilization,
which is a major issue when it comes to network maps.

### PHP Weathermap

[PHP Weathermap](http://network-weathermap.com/) is a network map tool, collecting data using various plugins. It is especially
well integrated with Cacti, but lacks support for Zabbix.

## Writing a PHP Weathermap plugin

#### Datasource

The main part of the plugin is the datasource, which fetches information about bandwidth utilization from Zabbix. It is a piece of code called by PHP Weathermap when generating a map, and supposed to convert a **TARGET** line into one or two float values.

#### Post-processing

This part runs through the generated map structure and enables altering the content. It can be used to modify the urls of graphs and links.

## Plugin features

### Datasource

You firstly have to configure the plugin, giving the url and the credentials to use to access Zabbix API. This is enough for the datasource.

{% highlight sh %}
SET zabbix_user username
SET zabbix_password password
SET zabbix_url http://zabbix/zabbix/api_jsonrpc.php
{% endhighlight %}

Then you just have to configure your links with a **TARGET** looking like this:

{% highlight sh %}
TARGET zabbix:name:switch-1:GigabitEthernet0/1-IN:GigabitEthernet0/1-OUT
{% endhighlight %}

And let PHP weathermap generate your map.

### Graphs

To be able to add graphs, besides the url and credentials, you also have to configure the url to use to display the graphs, and the graphs formatting.

{% highlight sh %}
SET post_zabbix_graphs 1
SET post_zabbix_graph_link 1
SET post_zabbix_graph_base_url https://zabbix/zabbix
SET post_zabbix_graph_width 420
SET post_zabbix_graph_height 150
SET post_zabbix_graph_period 86400
{% endhighlight %}

Then you can configure your graphs with **OVERLIBGRAPH** on nodes or links:

{% highlight sh %}
OVERLIBGRAPH zabbix:name:switch-2:CPU_usage
{% endhighlight %}

It will display the Zabbix graph when hovering the element, and make it a link to the Zabbix page of the graph.

### Example

Your weathermap should now display graphs:

{<2>}![Sample map](https://wiki.minet.net/_media/wiki/monitoring/weathermap.png?w=600&tok=91fcb0)

You can now even insert your weathermap inside a Zabbix *Screen* by specifiying its url.

## Documentation

You can find more information like an installation guide and configuration reference in the [documentation](https://github.com/amousset/php-weathermap-zabbix-plugin/wiki).
