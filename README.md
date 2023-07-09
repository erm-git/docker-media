Forked From EZARR
- [EZARR](https://github.com/Luctia/ezarr)

# erm-git docker-mediastack

## docker-mediastack
- Proxmox Debian Unprivaleged LXC running Docker.
- Media and Torrent data stored on ZFS Pool "tank" on Proxmox host.
- ZFS pool mounted on proxmox host
- Using uid/gid bindmounts 1000:1000 to mount zfs pool on LXC guests
- Using Hardlinks for "Atomic Moves"

### Manage/View Media
- [Sonarr](https://sonarr.tv/) 
- [Radarr](https://radarr.video/) 
- [Lidarr](https://lidarr.audio/) 
- [Readarr](https://readarr.com/) 
- [Mylar3](https://github.com/mylar3/mylar3) 
- [Audiobookshelf](https://www.audiobookshelf.org/)
- [Prowlarr](https://wiki.servarr.com/prowlarr) **Note**: When adding an indexer, set the "seed ratio" to 1.
- [PleX](https://www.plex.tv/) is a mediaserver. Using this, you get access to a Netflix-like
  interface across many devices like your laptop or computer, your phone, your TV and more. For
  some features, you need a [PleX pass](https://www.plex.tv/nl/plex-pass/).
- [Tautulli](https://tautulli.com/) is a monitoring application for PleX  which can keep track of
  what has been watched, who watched it, when and where they watched it, and how it was watched.
- [Overseerr](https://overseerr.dev/) is a show and movie request management and media discovery
   tool.

### Download Clients (not using docker for now)
- [qBittorrent](https://www.qbittorrent.org/)
- [Private Internet Access](https://www.privateinternetaccess.com/) using [pia-wg](https://github.com/triffid/pia-wg) scripts **Note**: Need to figure out how to include these scripts in the qbittorrent image/container so that qBittorrent traffice only uses the PIA network interface. For now I have natively installed qBittorent on another LXC.


## Using
### Using the CLI
To make things easier, a CLI has been developed. First, clone the repository in a directory of your
choosing. You can run it by entering `python main.py` and the CLI will guide you through the
process. Please take a look at [important notes](#important-notes) before you continue.

### Manually
1. To get started, clone the repository in a directory of your choosing. **Note: this will be where
   your installation and media will be as well, so think about this a bit.**
2. Copy `.env.sample` to a real `.env` by running `$ cp .env.sample .env`.
3. Set the environment variables to your liking. Note that `ROOT_DIR` should be the directory you
   have cloned this in.
4. Run `setup.sh` as superuser. This will set up your users, a system of directories, ensure
   permissions are set correctly and sets some more environment variables for docker compose.
5. Take a look at the `docker-compose.yml` file. If there are services you would like to ignore
   (for example, running PleX and Jellyfin at the same time is a bit unusual), you can comment them
   out by placing `#` in front of the lines. This ensures they are ignored by Docker compose.
6. Run `docker compose up`.

That's it! Your containers are now up and you can continue to set up the settings in them. Please
take a look at [important notes](#important-notes) before you continue.

## Important notes
- When linking one service to another, remember to use the container name instead of `localhost`.
- Please set the settings of the -arr containers as soon as possible to the following (use
  advanced):
  - Media management:
    - Use hardlinks instead of Copy: `true`
    - Root folder: `/data/media/` and then tv, movies or music depending on service
  - Make sure to set a username and password for all servarr services and qBittorrent!
- In qBittorrent, after connecting it to the -arr services, you can indicate it should move
  torrents in certain categories to certain directories, like torrents in the `radarr` category
  to `/data/torrents/movies`. You should do this. Also set the `Default Save Path` to
  `/data/torrents`. Set "Run external program on torrent completion" to true and enter this in the
  field: `chmod -R 775 "%F/"`.
- You'll have to add indexers in Prowlarr by hand. Use Prowlarrs settings to connect it to the
  other -arr apps.
  
### SABnzbd External internet access denied message
When you're trying to access SABnzbd the first time you'll come across the message `External
internet access denied`. To fix this simple modify the `sabnzbd.ini` and change `inet_exposure` to
`4`, restart the docker container for sabnzbd (`docker restart sabnzbd`) and now you can access the
UI of SABnzbd (note: you may get a `Access denied - Hostname verification failed`, to fix this,
simply go to the IP of your server directly instead of the hostname). After accessing the UI don't
forget to set a username and password (https://sabnzbd.org/wiki/configuration/3.7/general,
section Security).

For more instructions or help see also https://sabnzbd.org/wiki/extra/access-denied.html on the
official SABnzbd website.

## FAQ

### Why do I need to set some settings myself, can that be added?
Some settings, particularly for the Servarr suite, are set in databases. While it *might* be
possible to interact with this database after creation, I'd rather not touch these. It's not
that difficult to set them yourself, and quite difficult to do it automatically. For other
containers, configuration files are automatically generated, so these are more easily edited,
but I currently don't believe this is worth the effort.

On top of the above, connecting the containers above would mean setting a password and creating an
API key for all of them. This would lead to everyone using Ezarr having the same API key and user/
password combination. Personally, I'd rather trust users to figure this out on their own rather
than trusting them to change these passwords and keys.
