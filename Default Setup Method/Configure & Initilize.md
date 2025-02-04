After downloading, installing, and activating GProgress, it is now time to configure and initialize. This customizes the plugin for your project and performs the initial settings.

# Configuration
After activating the plugin, a panel is added to the bottom panel of the editor, which is designed for setting up the plugin using a user interface that is displayed by clicking on _GProgress_ in the lower 
box of the screen.

> [!Warning]
> **Never** change the settings after the first configuration! **The settings are not designed for this purpose**. If changes are necessary, **delete all progress files and backups**; ignoring this may cause 
disruptions!

## User Settings
In [GProgress](https://mkh-user.github.io/GProgress/), a player is referred to as a **user** (because this plugin also supports software developed with Godot and effectively manages the history of changes 
made in editor programs (Undo/Redo)!). In the settings menu, there are three tabs, with the first tab being **User Settings**, which provides three options.

### User Slots
One of the most important features of GProgress is its support for multiple users playing (or using) the game separately. Each individual can create a user account whose data is separate from other users. 
This option specifies the maximum number of allowed users; slots can be empty, but you cannot define more users than the specified number. You can set any positive integer value for it.

> [!NOTE]
> **Frequently Asked Question:** Why aren't the slots unlimited?
> 
> **Answer:** For three reasons:
> 1. If the number of slots is unlimited, designing the user interface for selecting users becomes difficult.
> 2. More slots mean more storage space is needed for saving game data; GProgress uses compression, but there is no guarantee that after the first 10 users, storage space will remain optimized, and this
>    significantly slows down the program.
> 3. GProgress uses a simple `UUID (Unique User ID)` to keep users separate while simplifying game development; if the number of users increases, there is a potential for UUID conflicts. Therefore, it is recommended
>    to keep the number of slots low; essentially, no device needs to support more than 10 users!

### User Parameters
In addition to the ability to save multiple progress files for each user, a user can have parameters that are separate from their progress. These parameters are kept exclusively alongside the user and can 
be easily retrieved or changed; you can have values like `level`, `name` (default), `profile`, `best_score`, or similar, which can be accessed without needing to open the progresses. This allows for greater 
customization for the user (if you wish) and provides optimized categorization and management of users. This list must be a text separated by commas (**all spaces are automatically removed**, so `random item` 
and `randomitem` will be considered the same, and if both are entered, it will be deemed a duplicate; however, when calling the parameter, **you must name it `randomitem`!**)

> [!Important]
> You can delete any of the existing default parameters, but deleting the `id` parameter will cause an error and disable the plugin!

### Limitations for Each User
**In GProgress, each saved data related to the game's state (or player) is called progress.** Each user can have many different progresses that are completely independent. To optimize storage space 
(and maintain maximum program speed), GProgress can limit the number of progresses for each user. When a new progress is saved and the number of saves exceeds the allowed limit, the last save 
(based on `UPID (Unique Progress ID)`, which is time-dependent) will be deleted. You can set this to `0` to make it unlimited, but as with previous cases, **this is not recommended**.

## Progress Settings
As you know, in GProgress, the stored data from the game state is referred to as progress. Progresses hold data that can fully restore the game state to the time of saving the progress.

### Progress Parameters
Each progress is saved as a file (which is encrypted and compressed by default), and the plugin uses a dictionary with specific keys when saving this files. These keys refer to data values that indicate 
the details of the game state. For example, if the only important thing to save in the game is the player's position on a 2D world, we define a key named `player_position` (we will review how to use it in 
later tutorials). This section specifies the parameters of each progress, a comma-separated list that ignores spaces. To specify the items in this list, you should have a clear idea of your game to know 
exactly what you will save; **it is recommended not to delete the defaults**, as removing `id` and `name` will certainly lead to an error, and other default parameters may disrupt some processes of the plugin!

### Preview Parameters
One of GProgress's capabilities is categorizing and packing progresses into specific formats for the user to design a list to load progresses. Previews provide a quick and ready way to design the user 
interface; for example, there is a ready-made function in GProgress to retrieve a list with a specified number of previews of saved progresses (we will explore their applications further in later tutorials). 
This section is also a comma-separated list that ignores spaces, and **its items should be selected from the previous section**.

## Save and Backup Settings
The settings in this section apply to progresses, managing some of GProgress's most important special features.

### AutoSave Interval
Automatic saving is one of the features that GProgress offers virtually; automatic saves _are not completely automatic_ but require designing a **GPClient** (we will examine how to design it in later tutorials).

### Save Location
Progresses will be stored in this location; **it is highly recommended to set it to a folder in `user://`** [(see here for why)](https://docs.godotengine.org/en/stable/tutorials/io/data_paths.html#accessing-persistent-user-data-user).

### Backup Interval
Backups are encrypted files that maintain a progress in a different formatting; in case of issues in the save folder or disruption in a progress file, they can be converted to a progress and added to the 
user's progresses for restore game/app.

### Backup Location
It is recommended to keep backups in a completely separate folder from the progresses folder to remain safe in case of problems (as set by default).

> [!TIp]
> it is highly recommended to set it to a folder in `user://`! [(see here for why)](https://docs.godotengine.org/en/stable/tutorials/io/data_paths.html#accessing-persistent-user-data-user)

### Compression
Progress files are compressed by default to take up less space; this plugin uses an optimal standard method for compression to ensure that process speed is not diminished, but you can disable this option.

### Encryption
One of GProgress's important features is the encryption of progresses; this encryption is optional, and you can disable it, but doing so may allow the manipulation of progress files to disrupt your program. 
This feature is customized with the next option.

### Encryption Key
This key is an access code for the progresses, which will be automatically used by the plugin for encrypting and decrypting them. **It must be 32 characters long**; otherwise, empty characters will be filled 
with `?`.

## Settings Management Options
At the bottom of the settings panel and independent of the tabs, there are three options. The first option opens the plugin's settings `.txt` file, allowing you to change it manually (some characters have 
specific meanings). Use the second option after making changes to identify changes in the file so that they are stored in the dedicated memory. The last option resets the settings to default.

# Initial Activation
To activate the plugin, you need to add a line to your game's main scene (the first scene that runs):
```gdscript
#under _ready()
GPro.is_initialized(true)
```
This line checks the readiness of the plugin; true means that if the initial activation has not been done, it will automatically perform it. If the initial activation has not been done, a warning is sent 
to the debugger (console in exported applications) each time the program is run. Since initial activation during program execution may lead to errors, we recommend using this line to reload the main page 
after activation automaticly:
```gdscript
#under _ready()
if not GPro.is_initialized(true): get_tree().reload_current_scene()
```
This line is executed only once for each device, and in addition, you can check if this is the first game the program is running on the device with `GPro.is_initialized()`. 
> [!Note] 
> This line checks for the existence of a file in `user://` and the activation will also create this file upon success, so any change that causes the content of this folder to be lost will revert the plugin
> activation to its initial state. 

> [!Important] 
> Here, **`device`** does not mean a PC, laptop or mobile, as each **user account in operating system** has a separate folder for `user://`, consequently, if the program is opened under a different user
> account (from the user accounts of the operating system), program will not have access to the information of other user accounts, including progress, backups, plugin settings files, and anything similar
> (if the progress and backups are defined in the `user://` folder).


# Previous step: [Installing](https://mkh-user.github.io/GProgress-Demos/Default%20Setup%20Method/Installing)
# Next step: Setup Users Menu
