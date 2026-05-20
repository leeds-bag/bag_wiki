## Easy OneDrive syncing on linux machines


### Setting up Rclone 
We can no longer mount OneDrive on the new Linux machines, but we can still use rclone to access OneDrive. You can setup OneDrive by running 
```bash
rclone config
``` 
and following the instruction to install it.

### Simple Rclone commands
There are many useful rclone commands that you can use from the command line including 
```bash
# list all files in onedrive
rclone ls onedrive: 
# copy a file to onedrive Pictures folder
rclone copy /home/user/photo.jpg onedrive:Pictures
# download a folder from the cloud
rclone copy onedrive:ProjectFiles /home/user/Projects
# synchronise a local folder with the cloud version
rclone sync /home/user/Music onedrive:Music
```
> [!WARNING]
> But watch out! If you locally delete a file then "sync" it will be deleted from OneDrive (although you can restore deleted files on OneDrive)


### Syncing script
To make synchronising files safe and easy I collaborated with an AI to make a script called [figsync](https://github.com/leeds-bag/bag_wiki/blob/main/scripts/figsync). This script lets you sync any local folder with any OneDrive folder. For each pair of folders you define an alias to make it quick and easy to sync.

#### Setup
1. copy [the script](https://github.com/leeds-bag/bag_wiki/blob/main/scripts/figsync) and paste it into a file called `figsync` (or anything else) in `~/.local/bin/`
2. `chmod 777` the script so it can be run from the command line
3. Point `figsync` at your local folder of figures (or anything else) by editing `LOCAL_BASE` at the top of the file. In that folder, create folders where you can save figures. Each folder name can be an alias for each of your different projects.
4. create a file `touch ~/.sync_aliases` where you define the folders and their aliases e.g.
```bash
trf:hardknott/aviva/projects/temperate_forestry/figs
heatwaves:craft/figs/heatwaves
ibutton:hardknott/figs/ibutton
elev:hardknott/woodland_elevation
```
These point to the folders on OneDrive to sync to.

#### Usage
For example, if I want to sync my `trf` folder, I simply run on the command line:
`figsync sync trf`
Or if I want to pull (OneDrive to local) or push (local to OneDrive) only I can do
`figsync pull trf` or `figsync push trf`

The script will automatically warn you and require input if files will be deleted as a result. You can also run it with all of the following optional flags:

```bash
Options:
    -y, --yes       Non-interactive mode (default)
    -a, --ask       Ask before destructive sync flows
    -n, --dry-run   Preview changes without writing
    -u, --update-only
                     Skip overwriting newer destination files
    -v, --verbose   Show rclone transfer progress
    -q, --quiet     Compact check output (summary only)
    -h, --help      Show this help message
```

