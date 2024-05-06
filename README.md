# How to set up dev environment?

To compile modied firmware I did this:
* fork this repo on github https://github.com/moergo-sc/zmk
* and this repo https://github.com/moergo-sc/glove80-zmk-config (actually use it as template)
* in config repository there is a build.yml file, and you need to change it to your forked zmk repo. See this commit: https://github.com/valdur/zmk-glove80-config/commit/5f07dc8fae5c4554d0b79a8a0c7305159996092a
* I used github codespaces via visual studio code to edit both repos - it's quite handy (originally, after a while I just downloaded the repos and work on them locally)
* for the layout I still use the web configurator tool - I download "ZMK keymap" (instead of pressing "Build Firmware") and copy paste it's content to zmk-glove80-config/config/glove80.keymap
* after making changes in either repo I build firmware through github actions (https://github.com/valdur/zmk-glove80-config/actions/workflows/build.yml - in your own repo you'll be able to run, not only view)


# Good to know:

* If you want to see what changed compared to MoErgo repo, have a look here: https://github.com/moergo-sc/zmk/compare/main...valdur:zmk-glove80:main
* This file contains the led map https://github.com/moergo-sc/zmk/blob/main/app/boards/arm/glove80/glove80_lh.dts It's  different than key order
* There is this constant in app/boards/arm/glove80/glove80_lh_defconfig
```
# DO NOT CHANGE CONFIG_ZMK_RGB_UNDERGLOW_BRT_MAX TO ABOVE 80. Configuring
# BRT_MAX above 80% will draw additional current and can potentially damage your
# computer. WARRANTY IS VOID IF BRT_MAX SET ABOVE 80.
CONFIG_ZMK_RGB_UNDERGLOW_BRT_MAX=80
```
which means that r, b and g values assigned to pixels array like this pixels[idx] = something;  should not exceed 80. Using HEXRGB macro is safe, writing (struct led_rgb){r : 255, g : 255, b : 255}; may or may not be fun 

* Per layer RGB patterns are implemented as fifth underglow effect, so you can toggle and have rainbow, swirl, breathe, single color or whatever they're called, and then there are per layer patterns.
* some code was added to send layer info through bluetooth from central to peripheral - I basically looked at each place where this was defined:
```
#if IS_ENABLED(CONFIG_ZMK_SPLIT_PERIPHERAL_HID_INDICATORS)
```
and added layer sync stuff there as well.

* An attempt was made to have a couple of underglew (ug) effects and a macro to swich layer and ug effect type at the same time, but in the end it turned out that if a behaviour (like rgb ug command) is fired in a macro it doesn't go through the code path that replicates behaviours on the peripheral, just locally

Original documentation below:

# MoErgo Glove80 Custom Configuration for ZMK

![MoErgo Logo](moergo_logo.png)

This repo is the official ZMK configuration of the MoErgo Glove80 wireless split contoured keyboard. Use it to develop your own keymap and easily build your own ZMK firmware to run on your Glove80.

**NOTE: You can also customize the layout of your Glove80 keyboard with the Glove80 Layout Editor webapp. For most users Glove80 Layout Editor is the recommended and simpler option. More information is available at the official MoErgo Glove80 Support site (see resources below).**

These steps will get you using your keymap on your keyboard in the fastest time possible. It uses the GitHub Actions feature to build your firmware online.

If you are looking to dig deeper into ZMK and develop new functionality, it is recommended to follow the steps of installing ZMK as found on the official ZMK documentation site (linked below).

## Resources
- The [official MoErgo Glove80 Support](https://moergo.com/glove80-support) web site. Glove80 documentation and other technical resources.
- The [official MoErgo Discord Server](https://moergo.com/discord). Instant conversations with other Glove80 users.

- The [official ZMK Documentation](https://zmk.dev/docs) web site. Find the answers to many of your questions about ZMK Firmware.
- The [official ZMK Discord Server](https://discord.gg/8cfMkQksSB). Instant conversations with other ZMK developers and users. Great technical resource!

- The [official Glove80 ZMK Distribution](https://github.com/moergo-sc/zmk). Repositiory for ZMK firmware customized for Glove80. 
 
## Instructions
1. Log into, or sign up for, your personal GitHub account.
2. Create your own repository using this repository as a template ([instructions](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)) and check it out on your local computer.
3. Edit the keymap file(s) to suit your needs
4. Commit and push your changes to your personal repo. Upon pushing it, GitHub Actions will start building a new version of your firmware with the updated keymap.

## Firmware Files
To locate your firmware files and reflash your Glove80...
1. log into GitHub and navigate to your personal config repository you just uploaded your keymap changes to.
2. Click "Actions" in the main navigation, and in the left navigation click the "Build" link.
3. Select the desired workflow run in the centre area of the page (based on date and time of the build you wish to use). You can also start a new build from this page by clicking the "Run workflow" button.
4. After clicking the desired workflow run, you should be presented with a section at the bottom of the page called "Artifacts". This section contains the results of your build, in a file called "glove80.uf2"
5. Download the glove80.uf2
6. Flash the firmware to Glove80 according to the user documentation on the official Glove80 Glove80 Support website (linked above)

Your keyboard is now ready to use.
