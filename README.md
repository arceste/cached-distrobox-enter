# cached-distrobox-enter
This is a very hack-and-slash script to cache the podman command that distrobox-enter generates.\
I put this together to solve an exceptionally minor issue I had with the distrobox-enter command. The amount of time I have spent on this script is highly disproportionate to the amount of time I actually lost to the minor issue that it solves.

## Story time
I run Bazzite on my Steam Deck which has been amazing, but I'm used to using mutable operating systems. I turned to [Distrobox](https://distrobox.it/) for software installation, which I subsequently found out was a wrapper around [podman](https://podman.io/). I specifically wanted to run the git version of Emacs (easily obtainable from the Arch's AUR) and so I went and installed it in there. However, when I went to go and run `emacsclient` using `distrobox-enter arch -- emacsclient -nc`, I noticed microtick of maybe half a second as the distrobox-enter command ran the command. The absolute horror.\
Through trial and error, I discovered that if I launched through podman directly I would see no such life-altering delay. However, I couldn't seem to get GUI apps to work as I'm not hugely smart and I didn't feel like following probably quite basic instructions at that time of night. Instead, I relied on the much more knowledgeable developers of Distrobox and their script to generate me a cacheable podman script!

## How to install it and make it work
Put it somewhere in your '$PATH' then run it. I recommend your `.local/bin` folder if you have that added. Alternatively, you can run it with `./distrobox-enter-c` all the time which would be annoying and tedious.
| Command | What it will do |
| :--- | ---: |
| `distrobox-enter-c <container-name> <command>` | Run the command you provide in the container you provide |
| `distrobox-enter-c <container-name>` | Runs `distrobox-enter <container-name>`. Not very exciting |
| `distrobox-enter-c` | Gives you an error. Probably could do something better, I dunno |

## What's actually happening
When `distrobox-enter` is run, it generates a `podman exec` command that is then executed. For my system, the command generation part of this script is what causes the delay. `distrobox-enter-c` rips the podman exec script out of a dry-run of distrobox-enter and dumps it into a file in `$HOME/.cache/distrobox` called `<containername>.cache`. It's actually an executable bash script (minus the +x flag though).\
The `distrobox-enter-c` will check to see if this file exists and how old it is. If the cached command is less than a week old *and* you already have the container running, `distrobox-enter-c` will execute that script instead of running `distrobox-enter`, saving you some precious milliseconds. If that exact precise set of conditions isn't met, then `distrobox-enter` is used instead and the cache is regenerated (except if no command is provided. I'm aware of this and will fix it at some point maybe). \
\
The script is pretty basic and so are my bash skills, so feel free to criticise. Here are some points to get you started:
- There's barely any error checking
- It doesn't account for certain use cases
- My regex probably could be better
- I haven't added enough comments
- It's inconsistent in how it works

If you want to fix those things, please absolutely feel free. Feel free to expand the above list too.
