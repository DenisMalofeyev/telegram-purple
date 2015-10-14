Telegram-Purple Unofficial (Beta 4)
===================================

Telegram-purple is a Libpurple protocol plugin that adds support for the Telegram messenger.

I keep getting many questions about this plugin in my E-mail, so I've created a
[telegram group chat](https://telegram.me/joinchat/01fb53f301b67d3c7a5532908dfa9a89) for
telegram-purple related discussions or questions.

Beta 4
------

    - fix some licensing issues by not depending on OpenSSL any longer
    - translation

Build
-----

Below, you will find the instructions for how to build the libpurple protocol plugin. If you just want to use the plugin in Adium, [download the precompiled packages here.](https://github.com/majn/telegram-purple/releases).

#### 1. Clone

This repository has submodules, so you need to clone recursively.


        git clone --recursive https://github.com/majn/telegram-purple
        cd telegram-purple


#### 2. Fetch all dependencies

##### Fedora

        sudo dnf install gcc gettext libgcrypt-devel libwebp-devel libpurple-devel zlib-devel


###### Debian / Ubuntu

        sudo apt-get install libgcrypt20-dev libpurple-dev libwebp-dev

###### OpenSUSE

        sudo zypper install gcc glib glib-devel libpurple libpurple-devel zlib-devel libwebp-devel

And the development files for gcrypt, probably `gcrypt-devel` or something.


#### 3. Compile and install

If libwebp is not available, you can disable sticker support by calling ./configure --disable-libweb instead.

        ./configure
        make
        sudo make install



Unofficial Packages
-------------------

##### Fedora 21, 22, 23 / CentOS7

https://copr.fedoraproject.org/coprs/eischmann/purple-telegram/


##### Arch AUR

https://aur.archlinux.org/packages/telegram-purple/


##### Ubuntu PPA (webupd8)

https://launchpad.net/~nilarimogard/+archive/ubuntu/webupd8



Pulling Updates
---------------

This repository contains submodules, and a simple pull just won't be enough to update all the submodules’ files. Pull new changes with:


        git pull
        git submodule update --recursive


Usage
-----

#### First Login

The username is your current phone number, including your full country prefix. For Germany, this would be '+49', resulting in a user name like '+49151123456'. Telegram will verify your phone number by sending you a code via sms. You will be prompted for this code, once that happens.

#### Buddy List 

Like in the official Telegram apps, the displayed buddy list consists of all active conversations.

##### Foreign Users

Foreign users, like for example people you only know through a group chat but never had any conversation with, will not be part of your buddy list by default. You can add those users to the buddy list by clicking on "Add contact..." in the users context menu.

#### Using secret chats

You can use Telegram secret chats with this plugin, they will show up as a new buddy with a '!' in front of the buddy name.

One caveat of secret chats in Telegram is that they can only have one endpoint, this is a limitation of the protocol. This means that if you create a secret chat in Pidgin you will not be able to use that chat on your phone. You will be asked whether to accept each secret chat, so you can always choose to accept the chat on a different device if you want. You can set a default behavior for dealing with secret chats (Accept or Decline) in the account settings, if you don't want that prompt to appear every time.

Self destructive messages will be ignored, since I don't know any way to delete them from the conversation and the history.

##### Confirming the key authenticity

Click on the buddy in the buddy list and click on "Show Info" to visualize the key fingerprint.  

##### Initiate secret chats

To initiate a secret chat from Pidgin, click on a Buddy in the Buddy List and hit "Start Secret Chat"

##### Deleting secret chats

If you delete a secret chat from the buddy list, it will be terminated and no longer be usable.


#### Unicode Emojis for Pidgin

The Telegram phone applications for iOS and Android make use of standardized Unicode smileys (called [Emojis](https://en.wikipedia.org/wiki/Emoji)). Pidgin
does not display those smileys natively, but you can install a custom smiley theme like (https://github.com/stv0g/unicode-emoji) or (https://github.com/VxJasonxV/emoji-for-pidgin) and activate it under Settings > Themes > Smiley Theme.


Building the Adium Plugin
-------------------------

Compiling with XCode is a little bit problematic, since it requires you to compile Adium first to get the necessary framework files. My advice is to just use the [prebuilt bundle](https://github.com/majn/telegram-purple/releases), but if you really want to do it, follow these steps:

1. Get the Adium source, compile it with XCode and copy the build output into telegram-adium/Frameworks/Adium. It should contain at least Adium.framework, AdiumLibpurple.framework and AIUitilies.framework
2. Open the Adium source code, go to ./Frameworks and copy libglib.framework and libpurple.framework into telegram-adium/Frameworks/Adium
3. Build the tgl submodule and delete libtgl.so from libs/ (it should only contain libtgl.a)
4. Install libwebp with homebrew and copy it into your project:

      brew install webp
      cp /usr/local/Cellar/webp/0.4.3/lib/libwebp.a ./telegram-adium/Frameworks/

5. Install libgcrypt with homebrew and make sure that libgcrypt.dylib is present in /usr/local/lib

      brew install libgcrypt

6. If you already downloaded libwebp/libgcrypt in previous builds make sure that the binaries are up-to-date

      brew update
      brew upgrade webp libgcrypt

7. Build the XCode-Project and execute the created bundle


Discussion / Help
-----------------

#### Custom pubkeys

As we want to avoid OpenSSL, it has become necessary to replace the PEM file format. This means that if you use a custom pubkey (which you really REALLY shouldn't be doing), you have to adapt, sorry.

We no longer ship `tg-server.pub` (old format), but instead `tg-server.tlgpub` (new format). If you have a `.pub` and want to continue using telegram-purple, please use this (hopefully highly portable) tool: [pem2bignum](https://github.com/BenWiederhake/pem2bignum)

You can also write your own conversion tool if you prefer. The format is really simple:

1. `e`, the public exponent, encoded as big endian 32 bit fixed length (e.g. `0x00 01 00 01` for 65537)
2. `n_len`, the length of `n` in bytes, encoded as big endian 32 bit fixed length (e.g. `0x00 00 01 00` for a 2048-bit = 256-byte key)
3. `n_raw`, the raw modulus, encoded as big endian, using the previously indicated length (e.g. `0xC1 50 02 3E [248 bytes omitted] 21 79 25 1F` in the case of telegram's public RSA key.)

If you are interested in developing a non-OpenSSL-licensed converter, look into [insane-triangle-banana](https://github.com/BenWiederhake/insane-triangle-banana).

#### Group chat

Telegram group chat for telegram-purple or libtgl related discussions or questions:

    - https://telegram.me/joinchat/01fb53f301b67d3c7a5532908dfa9a89


Empathy / libtelepathy
----------------------

Empathy doesn't natively support libpurple plugins since its based on libtelepathy, but there is a compatibillity layer called telepathy-haze that can be used to execute libpurple
plugins. This means that you can basically run this plugin thanks to telepathy-haze but you will usually get less features and worse usabillity compared to real libpurple clients. If you use Empathy (or anything else based on libtelepathy) I recommend [telepathy-morse](https://projects.kde.org/projects/playground/network/telepathy/telepathy-morse/repository) which is a connection manager written specifically for your messenger.

Authors
-------

Telegram-Purple was written by:

    - Matthias Jentsch <mtthsjntsch@gmail.com>
    - Vitaly Valtman
    - Ben Wiederhake
    - Christopher Althaus <althaus.christopher@gmail.com>
    - Markus Endres <endresma45241@th-nuernberg.de>


Acknowledgements
----------------

This software is based on the library [Libtgl](https://github.com/vysheng/tgl), which was written by Vitaly Valtman <mail@vysheng.ru> and others, see (http://github.com/vysheng/tgl)

For PNG rendering, it includes the [lodepng library](http://lodev.org/lodepng/).
