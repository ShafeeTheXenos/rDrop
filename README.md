
# rDrop: Dropbox interface via R

This package provides a  programmatic interface to [Dropbox](https://www2.dropbox.com/home) from the [R environment](http://www.r-project.org/).

> **Disclaimer: This package is fairly new and likely to contain bugs so please use discretion and report any issues here on github</u>**

**Important**: This package relies on `ROAuth 0.92` and the version currently available on CRAN does not play so well with `rDrop`. Install the version on Duncan's github:

```R
library(devtools)
install_github("ROAuth", "duncaltl")
```
**Without th newest `ROAuth` `rDrop` WILL NOT WORK!**

# Installing
```R
require(devtools)
install_github("rDrop", "karthikram")
```

# Initial setup
* To begin, create an `App` on Dropbox from the [Dropbox developer site](https://www2.dropbox.com/developers/apps). You will need to log in with your Dropbox username and password.Then, click **Create An App**.

![Create an app for your personal use on Dropbox](https://github.com/karthikram/rDrop/blob/master/screenshots/create_app.png?raw=true
)

* Next, provide a unique (in the universe of Dropbox apps) name to your personal app. Dropbox [branding guidelines](https://www2.dropbox.com/developers/reference/branding)  prohibit the use of the word **"Dropbox"** or names that begin with "**Drop**". We recommend that you name the app "**Your_first_name_last_name_rDrop**" to avoid naming conflicts but feel free to call it whatever you like. You won't have to deal with the app name after this step.


![Alt text](https://github.com/karthikram/rDrop/blob/master/screenshots/name_your_app.png?raw=true)

* Once you click create, be sure to **copy your App key and App secret** and store it somewhere safe. If you forget it, you can always find it [here](https://www.dropbox.com/developers/apps) (Just click on options next to your App name).  If you use your `.rprofile` and no one else uses your computer,  then we recommend you save your keys there by adding the following lines: <br><br>

```R
options(DropboxKey = "Your_App_key")
options(DropboxSecret = "Your_App_Secret")
```




![Alt text](https://github.com/karthikram/rDrop/blob/master/screenshots/keys.png?raw=true)

If you prefer not to specify keys in a `.rprofile` (especially if you are on a public computer/cluster/cloud server), you can specify both keys in the `dropbox_auth()` function directly (more below). <em>Note that once you have authorized an app, there is no need to call this function again.</em> You can just use your saved credential file to access your Dropbox. If for any reason, the file becomes compromised, just revoke access from your [list of authorized apps.](https://www2.dropbox.com/account#applications)

### Authorizing your app
```R
library(rDrop)
```

```R
# If you have Dropbox keys in your .rprofile, simply run:
 dropbox_credentials <- dropbox_auth()
# Otherwise:
 dropbox_credentials <- dropbox_auth("Your_consumer_key", "Your_consumer_secret")
```


If you entered valid keys, you will be directed to a secure Dropbox page on your browser asking you to authorize the app you just created. Click authorize to add this to your approved app list and return to R. This is a one time authorization. Once you have completed these steps, return to `R`. There is no need to run `dropbox_auth()` for each subsequent run. Simply save your credentials file to disk and load as needed:

```R
 save(dropbox_credentials, file="/path/to/my_dropbox_credentials.rdata")
```

Credentials will remain valid until you revoke them from your [Dropbox Apps page](https://www2.dropbox.com/developers/apps) by clicking the x next to your App's name.

# Quick User Guide
This package essentially provides standard Dropbox file operation functions (create/copy/move/restore/share) from within `R`.

To load a previously validated Dropbox credential file:

```R
load('/path/to/my_dropbox_credentials.rdata')
# or once again run,
dropbox_credentials <- dropbox_auth('Your_consumer_key', 'Your_consumer_secret')
```

### Summary of your Dropbox Account
```R
dropbox_acc_info(dropbox_credentials)
# will return a list with your display name, email, quota, referral URL, and country.
```

### Directory listing
```R
dropbox_dir(dropbox_credentials)
# To list files and folders in your Dropbox root.
dropbox_dir(dropbox_credentials, verbose = TRUE)
# for a complete listing (filename, revision, thumb, bytes, modified, path, and is_dir) with detailed information.
dropbox_dir(dropbox_credentials, path = 'folder_name')
# To see contents of a specified path.
dropbox_dir(dropbox_credentials, path = 'folder_name', verbose = TRUE)
# For verbose content listing for a specified path (relative to Dropbox root).
```


### Download files from your Dropbox account to R
```R
# Reading text files
file <- dropbox_get(dropbox_credentials, 'my_data.csv')
data <- read.csv(textConnection(file))

# Reading .rdata files
df <- data.frame(x=1:10, y=rnorm(10))
dropbox_save(dropbox_credentials, df, file="df.rdata")
# Now let's download this file back into R
rm(df)
downloaded_df <- unserialize(dropbox_get(dropbox_credentials, "df.rdata"))
# Another quick/dirty way to read private content from your Dropbox into R is using the dropbox_media() function.
# Example:
source <- dropbox_media(cred, 'test_works/move.txt')
read.csv(source$url)
```

### Upload R objects to your Dropbox
```R
# ext default is .rda.
a = 1:4
b = letters[1:3]
dropbox_save(dropbox_credentials, list(a,b), "duncan", verbose = TRUE, ext = ".rda")
```

### Moving files within Dropobx
```R
# Note that all paths are relative to your dropbox root. Leave blank or use / for root.
dropbox_move(dropbox_credentials, from_path, to_path)
# from_path can be a folder or file. to_path has to be a folder.
```

### Copying files within Dropbox
```R
# Note that all paths are relative to your dropbox root. Leave blank or use / for root.
dropbox_copy(dropbox_credentials, from_path, to_path)
# To overwrite existing file/folder in destination, add overwrite = TRUE.
```

### Creating a public share for any Dropbox file or folder
```R
dropbox_share(dropbox_credentials, file)
# File/folder to share. Returns share URL with expiration information.
# Link goes directly to files. Folder are automatically zipped up.
```

**Reference:**
[Complete Dropbox API Reference.](https://www2.dropbox.com/developers/reference/api)

