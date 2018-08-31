---
title: Converting Images to Video & GIF with FFMPEG
summary: Create a timelapse video or gif from still images utilising ffmpeg

excerpt_separator: <!--more-->

tags:
    - Powershell
    - Timelapse
    - ffmpeg
---

At my previous place of work I was asked to look into the feasability of adding a timelapse options to our service catalogue so that we could consolidate 3rd party usage and save money.

### Different Suppliers = Differing Costs and Varied Results

So as a company we were using different companies who all provided different equipment, different support models and services which then yielded different results on the output. So I started looking at how we could procure the equipment and provide a better service as standard utilising some existing infrastructure and some automation.

<!--more-->

I had used ffmpeg in the past and I was aware of some of it's capabilities so as part of the automation solution I looked to see if we were able to use it as a way of creating timelapse videos from the still images the cameras returned and the documentation is really concise: [ffmpeg Documentation](https://ffmpeg.org/ffmpeg.html)

I took one look at the documentation and decided I would google around for some examples instead because why RTFM!

I was able to come up with a POC solution which worked as intended and could be used from powershell.

### Setting up ffmpeg so that it was accessible to Powershell
Download the ffmpeg application from their site [Download Page](https://ffmpeg.org/download.html)

Extract the ffmpeg.exe application from the zipped folder and place it in your `PATH` you can find these values with `$env:Path -split ';'`

Now that the application is avaiable in your PATH you can call the application without having to fully qualify it e.g.

```powershell
# You can call the application directly
ffmpeg --help

# And don't need to fully qualify the Path to the .exe
C:\software\ffmpeg\ffmpeg.exe
```

### Solution / Code

Initally I created a really, really archaic wrapper function to get it to work

```powershell
function Invoke-ffmpeg {
    ffmpeg $args
}
```
This worked fine but you still had to know the syntax and the parameters required to be able to get the desired result and the syntax for ffmpeg isn't as easy to read as Powershell.

So recently I picked it up again to start looking at building something more concise. These are still very rough versions that have worked but don't take into account alot of differnet scenarios.

- Images have to be sequentially named ( I had the luxury of defining how the images were created so wasn't really an issue)
- Different present working directories may not be fully catered for
- I don't fully understand all of the encoding options that come with ffmpeg nor do I have the time or desire to learn them.

```powershell
function Invoke-ffmpeg {
    ffmpeg $args
}

function Convert-ImagesToVideo {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   Position=0)]
        [Alias("PSPath")]
        [ValidateNotNullOrEmpty()]
        [string]
        $ImagePath,

        [Parameter(Mandatory,
            Position = 1)]
        [int]
        $FrameRate,

        [Parameter(Mandatory = $false,
            Position = 2)]
        [string]
        $Resolution = '1920x1080',

        [Parameter(Mandatory, 
            Position = 3)]
        [string]
        $OutputFile
    )
    begin {
        Push-Location -Path $ImagePath
    }
    process {
        Invoke-ffmpeg -framerate $FrameRate -i "img%03d.jpg" -s $Resolution "$OutputFile.mp4"
    }
    end {
        Pop-Location
    }
}

function Convert-VideoToGif {
    [CmdletBinding()]
    Param (
        [Parameter(Mandatory,
                   Position=0)]
        [ValidateNotNullOrEmpty()]
        [string]
        $VideoFile,

        [Parameter(Mandatory = $false)]
        [int]
        $FrameRate,

        [Parameter(Mandatory,
                   Position=1)]
        [ValidateNotNullOrEmpty()]
        [string]
        $OutputFile
    )

    begin {
        $palette = ".\palette.png"
        $filters = "fps=$FrameRate,scale=480:-1:flags=lanczos"
        Invoke-ffmpeg -i $VideoFile -vf "$filters,palettegen" -y $Palette
    }
    process {
        Invoke-ffmpeg -i $VideoFile -i $Palette -lavfi "$filters [x]; [x][1:v] paletteuse" -y "$OutputFile.gif"
    }
    end {
        Remove-Item -Path $palette | Out-Null
    }
}
```
I tried to make some sample photos from my office window on what was apparently the quietest day in existence so you get my splendour to look at instead :D

```powershell
# Set the location of the folders, already named img001.jpg, img002.jpg etc....
Convert-ImagesToVideo -ImagePath .\ -FrameRate 24 -OutputFile myootput

# Convert that video to a gif.
Convert-VideoToGif -VideoFile .\myootput.mp4 -FrameRate 3 -OutputFile swankygif

```
#### Final Output
![My Swanky Gif](/assets/img/anotherone.gif)

#### I don't even have any of this on github but will create a repo to throw everything if anyone is interested.

Some Blog Posts I utilised for the syntax:
 - [Giphy Engineering - https://engineering.giphy.com/how-to-make-gifs-with-ffmpeg/](Giphy Engineering - https://engineering.giphy.com/how-to-make-gifs-with-ffmpeg/)
 - [Ubitux - http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html](Ubitux - http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html)