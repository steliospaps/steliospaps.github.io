# about
I use a Huawei AI Cube as my 4G broadband router (It came for free with my home 4G broadband connection.)
I got it prior to the COVID19 lockdown, when my only internet needs where streaming Netflix.

I was lucky enough to be able to work remotelly during the lockdown, but unfortunatelly the quality of the service deteriorated significantly in June to the extend that I could not reliably participate in Teams meetings.

Measured speeds dropped to about 5Mbits maximum with occasionally being as low as 0.5Mbits. I decided to record metrics of the network usage in order to exclude the possibility of a device hogging the network.

I already had influxdb and telegraf installed on a Raspberry Pi Zero W, and decided to use that.

This is a description of the solution I implemented.

# API
I goggled around for a description of the cube api, but the only link I found was [this project in gihub](https://github.com/HSPDev/Huawei-E5180-API/). It is a different model, but the '/api/monitoring/traffic-statistics' endpoints works on my router.

The payload looks something like:
```language=xml
<tag>
  <tag1>123</tag1>
  <tag2>456</tag2>
</tag>
```

In the example above I wanted my output to be:

```
tag1=123,tag2=456
```   

I could not be bothered figuring out an XPath extractor tool, so I used a cheap and dirty perl script to extract every tag with a numerical value from stdin.

```language=perl
perl -ne '
  while(/<(.*?)>([0-9]*)<\/\1>/g){
    push @items,"$1=$2";
  }
  END{
    print join(",",@items);
  }')
```

# telegram
I had telegram already installed on my Raspberry Pi Zero W, so I just made a script that uses curl to fetch values and output everything in influx data format:

```language=bash
#!/bin/bash
set -e #exit on error
set -o pipefail

ROUTER=${ROUTER:-http://192.168.8.1}

DATA=$(curl $ROUTER/api/monitoring/traffic-statistics --silent \
  | perl -ne '
  while(/<(.*?)>([0-9]*)<\/\1>/g){
    push @items,"$1=$2";
  }
  END{
    print join(",",@items);
  }')

echo "router $DATA"
```

I then configured telegram to invoke that script and persist the output (router is the name of the metric):

```
[[inputs.exec]]
  commands=["/home/username/bin/router-metrics-telegraf.sh"]
  name_override="router"
  data_format="influx"
```
# git repo
the code is [here](https://github.com/steliospaps/my-huawei-ai-cube-telegraf)
