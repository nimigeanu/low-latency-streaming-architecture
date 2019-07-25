# Autoscaling Low-Latency Streaming Architecture

## Features
* 2.5 seconds average end-to-end worldwide latency
* Royalty free solution - based on freeware [Nimble Streamer](https://wmspanel.com/nimble)
* RTMP ingest - broadcast with any capable software (i.e. OBS) or hardware (i.e. LiveU)
* All-AWS setup
* Auto scales to accomodate any audience; tested against hundreds of thousands 
* WebSocket egress; secure WebSockets supported

## Setup

### Deploying the architecture
1. Sign in to the [AWS Management Console](https://aws.amazon.com/console), then click the button below to launch the CloudFormation template. Alternatively you can [download](template.yaml) the template and adjust it to your needs.

[![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?stackName=low-latency&templateURL=https://s3.amazonaws.com/lostshadow/low-latency-streaming-architecture/template.yaml)

2. Adjust the parameters to fit your needs. Defaults are well suited for a production-grade architecture deployed in `us-east-1` (N.Virginia AWS region); see the notes below if you need to deploy to another region.  
Make note of the RTMP credentials (`RtmpUser` and `RtmpPassword`) as you will need these to set up RTMP broadcasting.
3. Check the `I acknowledge that AWS CloudFormation might create IAM resources` box. This confirms you agree to have some required IAM roles and policies created by *CloudFormation*.
4. Hit the `Create` button. 
5. Wait for the `Status` of your CloudFormation template to become `CREATE_COMPLETE`. Note that this may take **10 minutes** or more.
6. Under `Outputs`, notice the keys named `IngressPoint` and `EgressPoint`; write these down for using later

#### Notes:
* The default template values will only work in the *US East (N. Virginia)* Region; particularly, the `ImageId` being used is only valid for this region. To launch this solution in a different AWS Region, find out and use the corresponding AMI ID for the `Amazon Linux` instance type (e.g. for `us-west-1` this is `ami-015954d5e5548d13b`)

### Testing your setup

1. Point your RTMP broadcaster (any of [these](https://support.google.com/youtube/answer/2907883) will work) to the following URI:

		rtmp://{IngressPoint}/live/stream1
	...be sure to replace {IngressPoint} with the value output by CloudFormation above (step 6)

	Use the credentials provided above (step 2) for RTMP authentication (default `testuser`/`changeme`)

	Note that, while some RTMP broadcasters require a simple URI, others (like [OBS Studio](https://obsproject.com)) require a **Server** and **Stream key**. In this case, split the URI above at the last *slash* character, as following:
	
	**Server**: `rtmp://{IngressPoint}/live`  
	**Stream key**: `stream1`

2. Open the SLDP test player [here](http://player.wmspanel.com/#player=sldp) and set it up with the following URI

		ws://{EgressPoint}/live/stream1
	...be sure to replace {EgressPoint} with the value output by CloudFormation above (step 6)



### Integration

For browser playback, you'll have to set up and integrate the free SLDP player [here](https://softvelum.com/player/web)

To integrate with mobile apps you can make use of the commercial SDK [here](https://softvelum.com/mobile/buy)

## Notes
* Setup only proxies streams `stream1`, `stream2` and `stream3` from origin to edges; you will have to alter the template if you need more/less streams or if you want to name them differently
* The CloudFormation template creates a VPC with the internal IP range `10.107.0.0/16`; be sure to change this if it overlaps with any your other VPCs
