# Video Doorbell, Lab 7

*A lab report by Chris Brownell*

### In This Report

1. I do the first two parts of the lab
1. I forget the password on my pi as soon as I reset it and learn how to hack any pi I find lying around in the future
1. I do the third part of the lab

## Part A. HelloYou from the Raspberry Pi

**a. Link to a video of your HelloYou sketch running.**

[HelloYou video](https://drive.google.com/file/d/1w57RvpU60To6n9d8qm-bgLW2nO0xspis/view?usp=sharing)

## Part B. Web Camera

**a. Compare `helloYou/server.js` and `IDD-Fa18-Lab7/pictureServer.js`. What elements had to be added or changed to enable the web camera? (Hint: It might be good to know that there is a UNIX command called `diff` that compares files.)**

In pictureServer, we needed to add the webcam setup code:

```
//Default options
var opts = { //These Options define how the webcam is operated.
    //Picture related
    width: 1280, //size
    height: 720,
    quality: 100,
    //Delay to take shot
    delay: 0,
    //Save shots in memory
    saveShots: true,
    // [jpeg, png] support varies
    // Webcam.OutputTypes
    output: "jpeg",
    //Which camera to use
    //Use Webcam.list() for results
    //false for default device
    device: false,
    // [location, buffer, base64]
    // Webcam.CallbackReturnTypes
    callbackReturn: "location",
    //Logging
    verbose: false
};
var Webcam = NodeWebcam.create( opts ); //starting up the webcam
```

We also needed to add the new function takePicture to capture the picture, create a unique filename, and save the picture.

```
socket.on('takePicture', function() {
  /// First, we create a name for the new picture.
  /// The .replace() function removes all special characters from the date.
  /// This way we can use it as the filename.
  var imageName = new Date().toString().replace(/[&\/\\#,+()$~%.'":*?<>{}\s-]/g, '');

  console.log('making a making a picture at'+ imageName); // Second, the name is logged to the console.

  //Third, the picture is  taken and saved to the `public/`` folder
  NodeWebcam.capture('public/'+imageName, opts, function( err, data ) {
  io.emit('newPicture',(imageName+'.jpg')); ///Lastly, the new name is send to the client web browser.
  /// The browser will take this new name and load the picture from the public folder.
});

});
```

**b. Include a video of your working video doorbell**


To make the initial web camera doorbell, I just changed the light case in the server-msg function in client.js.

```
socket.on('server-msg', function(msg) {
  msg = msg.toString();
  console.log('msg:', msg);
  switch (msg) {
    case "light":
      console.log("taking pic from button");
      takePicture();
      break;
    default:
      break;
  }
```

[Webcam Doorbell Video](https://drive.google.com/file/d/1hYWb7co5jxEKvbpZb5S-vH0XjMLx6U64/view?usp=sharing)

## Part B2. Fun with passwords

I reset the password for pi at the tail end of lab on 10/18, and used a password that I promptly forgot. Then when I attempted to ssh into the pi at home, no luck.

I happened upon a [forum post](http://mapledyne.com/ideas/2015/8/4/reset-lost-admin-password-for-raspberry-pi) about how to reset, but did not have any micro-SD read/write capability. Or so I thought. After a long night sorting through old electronics I found an Android tablet with micro SD slot. Using Google Drive, I saved a new version of cmdline.txt (ending with init=/bin/sh) to the SD card so that the pi would boot into single user mode. Upon starting up the pi again, I did not get the confirmation email with the IP address, which was a good sign. Something was different!

Now I needed a monitor and keyboard to reset the pw. So I found one of each in the Bloomberg studio, powered on the pi, was met with 4 happy raspberries and a command prompt, and proceeded to re-mount the drive to read/write.

```
mount -rw -o remount /dev/mmcblk0pX /
```

Finally I was able to reset the password for the pi user. Using the android tablet, I restored the old version of cmdline.txt and was back in business! But by that time, Tuesday class was starting.

## Part C. Make it your own

**a. Find, install, and try out a node-based library and try to incorporate into your lab. Document your successes and failures (totally okay!) for your writeup. This will help others in class figure out cool new tools and capabilities.**

First I started by trying to use fswebcam to change the colors to greyscale, which worked, but I couldn't use the same approach to invert colors... so I decided to use jimp.

Fswebcam color:
![pic](https://github.com/chrisbrownell/IDD-Fa18-Lab7/blob/master/fswebcamcolor.png)

Fswebcam greyscale:
![pic](https://github.com/chrisbrownell/IDD-Fa18-Lab7/blob/master/fswebcamgreyscale.png)

Using jimp, I created a 4-box picture, including one standard image, one inverted, one pixelated (and slightly brighter) and one blurry (and slightly brighter).

Jimp 4-box:
![pic](https://github.com/chrisbrownell/IDD-Fa18-Lab7/blob/master/jimpfinal.png)

**b. Upload a video of your working modified project**

[Video](https://drive.google.com/file/d/1QEdHDuebCmarNS6MCJVZ4bxXaa_HJvPx/view?usp=sharing)
