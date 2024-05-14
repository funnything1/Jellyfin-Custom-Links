# Jellyfin-Custom-Links

I found a way to edit the forgot password button link and a way to add a requests button as a menu button that opens a new tab for use with jellyseer + jfa-go


There is another guide on how to do something similar but i found it did not achieve what i wanted. [jellyfin-mods](https://github.com/BobHasNoSoul/jellyfin-mods?tab=readme-ov-file#change-the-password-reset-process-for-use-with-jfa-go-email-password-reseting)

I use docker jellyfin but the steps should be very similar to windows/linux installs.

This is not an intended way to edit this. Any update to jellyfin may break something. I am using the latest jellyfin 10.9.1 as of 5/14/2024

# Instructions for forgot password link in jellyfin



To edit the forgot password functionality we first need to find the correct file that contains: ```e.querySelector(".btnForgotPassword")```

1. From within the docker container cd into "/jellyfin/jellyfin-web" the and run this command

```grep -rl "(".btnForgotPassword")" . 2>/dev/null```  This will output any files that contain the string "btnForgotPassword". The file I am looking for is something similar to ```session-login.xxxxxxxxxxxxxxxxxxxx.chunk.js```


2. Within that file find and delete this exactly: ```e.querySelector(".btnForgotPassword").addEventListener("click",(function(){m.ZP.navigate("forgotpassword.html")})),```    This stops the click action from redirecting to the builtin forgotpassword.html page.

Next we have to find "ButtonForgotPassword" for the html part

3. ```grep -rl "ButtonForgotPassword" . 2>/dev/null```  For me this found 2 files the one i needed to edit was ```en-us-json.aed814678aaadfab3be8.chunk.js``` it might be different for you. 

4. Within that file we need to edit "ButtonForgotPassword":"Forgot Password" to become a clickable link. Here is what worked for me:
```"ButtonForgotPassword":"<a href=\'CUSTOM URL HERE\' style=\'position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; cursor: pointer; text-decoration: none; color: inherit;\'></a>Forgot Password"```

# Result:
Clicking forgot password takes you to our cutom link like to jfa-go
![image](https://github.com/funnything1/Jellyfin-Custom-Links/assets/69848819/0c12e45c-5edc-4021-8cd0-e69f2bc18523)
![image](https://github.com/funnything1/Jellyfin-Custom-Links/assets/69848819/97e7a409-4b4c-431b-b591-348c05d093ea)




# Instructions for adding custom tab link + sidebar link


It is a very similar process to editing the forgot password link.
1. from within /jellyfin/jellyfin-web find the file containing ```{name:u.XX.translate("Favorites")}```
   
   ```grep -rl ""Favorites"" . 2>/dev/null``` the file i needed to edit was similar to ```home.4b3e34ee8d1232d66dec.chunk.js```
   
3. Within that file find something similar to this: "{name:u.XX.translate("Favorites")}" and right after that add ```,{name:u.XX.translate("<a href=\'CUSTOM URL HERE\' rel=\'noopener noreferrer\' target=\'_blank\' style=\'position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 165%; height: 340%; z-index: 1; cursor: pointer; text-decoration: none; color: inherit;\' class=\'emby-tab-button\'></a>Requests")}``` Where XX is 2 letters that you will find next to translate("Favorites") this will need to be the same letters that were there origionally.

4. (Optional) adding sidebar links
   
 Jellyfin has a guide on how to do this:
 [Jellyfin Web Configuration](https://jellyfin.org/docs/general/clients/web-config/)



# Result:
Clicking the request button opens a new tab with our own link such as jellyseer
![image](https://github.com/funnything1/Jellyfin-Custom-Links/assets/69848819/48debc98-8fc4-4be6-95c7-e2c91644303d)
![image](https://github.com/funnything1/Jellyfin-Custom-Links/assets/69848819/8cd11a01-307d-42cc-bfc5-e44c72f33ea9)

# Making changes persistant in docker
Since i am using docker any changes i make wont be persistant so i will need to move each of my edited files to another location then link them in the docker.
I accomplished moving the files outside of the docker environment by using scp into another location then linking the new location to where they are inside of docker

I am using truenas but it would be similar for any docker compose

![image](https://github.com/funnything1/Jellyfin-Custom-Links/assets/69848819/91a7726a-1833-49e6-976e-4a4eef0bf4c5)

# Notes:
These changes are only able to be used inside of jellyfin web and none of these changes will affect jellfin media player or any dedicated jellyfin app.

They will however affect the iphone and andriod jellyfin apps which is nice.



