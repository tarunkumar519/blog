---
title: "Customise Your Jellyfin Media Server"
date: 2023-01-06T14:48:47+05:30
draft: false
tags: ["media-server", "jellyfin"]
categories: ["selfhosting"]
cover:
    image: /customise-your-jellyfin-media-server.png
    alt: 'Jellyfin Home Screen'
    caption: 'Jellyfin is a FOSS Media Server'
---

Jellyfin is an awesome Open-Source Media Server for everyone. It is used by most people today who switched from other media servers like Plex or Emby. Many don't like the way Plex is heading.

If you have already setup Jellyfin and enjoying iy, you may want to check the below customisations for Jellyfin to enhance your viewing experience.

## Display custom message

You can Display custom messages on all Jellyfin Web based clients like Jellyfin web, Jellyfin Media Player on Windows, Jellyfin Android, iOS etc. This might be useful if you want to display server downtime messages to all your users. I also use this to display which content is leaving soon and leaving today just like Netflix. 

![mobile image](/mobile-message.png)
You can achieve this by pasting the custom css code from below in the Custom CSS blank inside Jellyfin Server. You can make changes to this as you like for setting your own Font, color, size and whatnot.


```.emby-tabs-slider::before { content: "Server Maintenance at 6AM"; color: red; font-weight: bold; position: absolute; top: 0.5em; left: 0; width: 100%; text-align: center; } .emby-tab-button { padding-top: 2.5em; } .libraryPage { padding-top: 8.5em !important; }```

You can change message in `content:` , just change color from `red` to any other you like. You may want to fiddle with `top` , `padding` to set how high or low message appears. Make sure it does not collide with top menu bar of Jellyfin.

## Skip Intros like Plex

One cool thing in Plex (only for plex pass subscribers) is how it automatically analyzes the files and finds where the intros are to TV-shows. It will show 'Skip Intro' button just like Netflix. Well. Jellyfin has a plugin for that !

![skip button](/skip-button.png)

Intro Skipper plugin from [ConfusedPolarBear](https://github.com/ConfusedPolarBear) does exactly that. You need to follow instructions [here](https://github.com/ConfusedPolarBear/intro-skipper) and install the modifidied web interface just like it says. You can always just delete the web interface folder if maybe it does'nt work for you. Your Jellyfin install will be fine.

One more thing, if you have portainer or docker you might follow thi step from those instructions.

```services:
    jellyfin:
        ports:
            - '8096:8096'
        volumes:
            - '/full/path/to/extracted/dist:/jellyfin/jellyfin-web:ro'  # <== add this line if using the official container
            - '/full/path/to/extracted/dist:/usr/share/jellyfin/web:ro' # <== add this line if using the linuxserver container
            - '/config:/config'
            - '/media:/media:ro'
        image: 'jellyfin/jellyfin:10.8.0'
```

Here `:ro` at the end of volumes path means ‘readonly’. Just paste everything as it is and it will work.

After you followed through all the steps and restarted the Jellyfin server, you can select 'Detect Introductions' task and it will finish in a few minutes. This task is a little CPU, RAM intensive so if you're on lower end devices like a Raspberry Pi make sure you're not doing any other tasks on the server so there is enough RAM and CPU usage available. But a Pi 4 is enough for this task and it will absolutely do it, rest assured.

You might want to download modified Jellyfin Media Player with Intro Skip button built in on Windows from fallenbagel [here](https://github.com/Fallenbagel/jellyfin-media-player/releases). Props to his work.

If you go into Intro Skipper plugin settings you can even select the specific TV episode and select specific time manually right there to show skip intro button !

## Play Trailers before Movies

Plex, Emby (only with Emby premiere subscription) plays trailers of Movies, Show in your library or trailers of upcoming movies, shows. This will give a theater like experience right at your home and also help people using your server discover more movies, shows on the server.

Well, Jellyfin can do this too !

There is a plugin called jellyfin-plugin-intros from [dkanada](https://github.com/dkanada), you can get it [here](https://github.com/dkanada/jellyfin-plugin-intros). Follow the instructions in there to install it. This plugin is designed to play intros only from the website prerolls.video or any video from vimeo. Well, there are many trailers, TV spots for Movies, Shows on Vimeo already, We can upload our own TV-sports and trailers to Vimeo too and put those into this plugin.

Vimeo provides 5GB free storage which is plenty for just TV-spots or Trailers stuff. You upload something and get the Vimeo ID from the URL or share URL for the vimeo video.

You just put all vimeo ids seperated with a `,`(comma) like this `1354655,6565376,7675425` and it automatically selects a random id and plays it. This requires active internet connection though. There is also option to store videos offline and play them but i saw many people having issues with it. You can try it if you want. 

## Slick new UI for Jellyfin

Want to try a new UI for entire Jellyfin ?  [jellyfin-vue](https://github.com/jellyfin/jellyfin-vue) is the answer. Props to them for making such a cool UI using Vue. This might come as Official Jellyfin UI in the future, who knows. But you can try it right now using the instrutions given there.

It would really help me motivate to write more self hosted content by donating. [Buy me a cup of Coffee here](https://buymeacoffee.com/tharunx)