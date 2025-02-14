FROM gitpod/workspace-full

USER gitpod
WORKDIR /home/gitpod

RUN git clone https://github.com/emacs-mirror/emacs --depth 1 --branch feature/pgtk
WORKDIR /home/gitpod/emacs/
RUN sudo apt-get update && sudo apt-get install -y build-essential git autoconf texinfo libgnutls28-dev libxml2-dev libncurses5-dev libjansson-dev
RUN sudo apt-get install libgtk-3-dev:amd64 -y
RUN sudo apt-get install libgccjit-9-dev -y
RUN ./autogen.sh
RUN ./configure --with-pgtk --with-cairo --with-modules --with-native-compilation --with-json
RUN NATIVE_FULL_AOT=1 make -j8

WORKDIR /home/gitpod/

RUN mkdir -p ~/.local/share/fonts/
COPY fonts/* ~/.local/share/fonts/

COPY configs/install-packages.el /tmp/install-packages.el
RUN ~/emacs/src/emacs --batch -l /tmp/install-packages.el

COPY configs/install-servers.el /tmp/install-servers.el
RUN ~/emacs/src/emacs --batch -l /tmp/install-servers.el

RUN mv ~/.emacs.d/ ~/vanilla

COPY configs/vanilla/init.el /home/gitpod/vanilla/init.el

RUN git clone --depth 1 https://github.com/syl20bnr/spacemacs /home/gitpod/.emacs.d/ --branch develop
COPY configs/spacemacs.d /home/gitpod/.spacemacs.d

RUN SPACEMACSDIR=~/.spacemacs.d ~/emacs/src/emacs --batch -l ~/.emacs.d/init.el
RUN mv ~/.emacs.d ~/spacemacs

RUN echo export SPACEMACSDIR=~/.spacemacs.d >> ~/.bashrc

# Doom
## Clone and set the DOOMDIR folder
RUN git clone --depth 1 https://github.com/hlissner/doom-emacs /home/gitpod/.emacs.d/ --branch develop
COPY configs/doom /home/gitpod/.config/doom

## Sync to install modules and packages
RUN ulimit -n 9000
RUN EMACS=~/emacs/src/emacs ~/.emacs.d/bin/doom sync

## Move Doom to Chemacs profile
RUN mv ~/.emacs.d ~/doom

COPY configs/.emacs-profiles.el /home/gitpod/.emacs-profiles.el

RUN git clone --depth 1 https://github.com/plexus/chemacs2 /home/gitpod/.emacs.d/
