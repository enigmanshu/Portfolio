---
layout: post
title: Raspberry Pi Camera and Telegram Bots
description: "Taking a picture on Raspberry Pi camera and sending to Telegram using Telegram Bots."
modified: 2015-08-15
tags: [python, telegram, bot, raspberry pi, raspberry pi camera]
image:
  feature: secureCam.jpg
  credit:
  creditlink:
---

I find [https://telegram.org](Telegram) one of the best instant messengers currently in the market. They are secure, fast, free and open source. They also offer [https://core.telegram.org/bots](bots API) to make bots of your own. You can program the bots in any of the major languages. The open source community has shown a great enthusiasm and built complete packages to make it easier to make these bots. I have used one such packages, [https://github.com/python-telegram-bot/python-telegram-bot](python-telegram-bot) which is, obviously, a python package.

I have created a simple bot which just takes the picture and sends it to the user who asks for the picture. I wanted to make a surveillance camera hence I choose [http://amzn.to/2bt9DFR](Raspberry pi), because it's credit card sized computer which makes it more portable and easy to install anywhere and consumes very low power. Pairing it with the [http://amzn.to/2aUZy5w](Raspberry Pi camera board) to shoot images.

To get started with Raspberry Pi you can follow [https://www.raspberrypi.org/wp-content/uploads/2012/12/quick-start-guide-v1.1.pdf](this link)
To know how to use camera go [https://www.raspberrypi.org/learning/getting-started-with-picamera/worksheet/](here)

Once done with this now you are ready to get started with making your own surveillance camera.

I have used here two python packages [https://picamera.readthedocs.io/en/release-1.12/](picamera) and [https://github.com/python-telegram-bot/python-telegram-bot](python-telegram-bot).

To get started with this tutorial you'll need to create your Bot first. This can be done by using the telegram app itself on any platform. Open your telegram app and search for `@BotFather`, open the user chat and click on `start`. Now the Bot Father will help you create a bot in an interactive way. When done with creating your Bot, you would have a received a token. Save that token.

With all the above processes done, now you are ready to build your surveillance camera.

Now go ahead and fork [https://github.com/enigmanshu/TelegramBots](my repo). If you are using a ssh session transfer `secureCam.py` to your RaspberryPi using any FTP client like [https://filezilla-project.org](FileZilla). Edit the script and replace your bots token with `TOKEN` on line 62 of the script.

Run the script using `python /path/to/file/secureCam.py`. Once running, go to your telegram app, search for your bot and start it. Send `/photo` and voila !! You'll have the snap.

To modify the settings and tune the camera settings add or modify the `click_picture()`. Go [https://picamera.readthedocs.io/en/release-1.12/api_camera.html#picamera](here) to know more about how to control camera settings.

Now go and deploy the pi at the strategic location and spy.

Cheers !!
