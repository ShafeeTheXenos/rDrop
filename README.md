# rDrop: Dropbox interface via R

This package provides a  programmatic interface to [Dropbox](https://www2.dropbox.com/home) from the [R environment](http://www.r-project.org/).

> **Disclaimer: This package is currently in development so please <u>use at your own risk.</u>**

**Important**: This package relies on `ROAuth` and the version currently available on CRAN does not play so well with `rDrop`. Use [ROAuth_0.92.0 version](http://dl.dropbox.com/u/2223411/ROAuth_0.92.0.tar.gz) and install from source for the time being till issues get patched up.

**Reference:**
[Complete Dropbox API Reference.](https://www2.dropbox.com/developers/reference/api)

# Installing
```
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
<pre><code>
options(DropboxKey = "Your_App_key")
options(DropboxSecret = "Your_App_Secret")
</code></pre>
<br>

![Alt text](https://github.com/karthikram/rDrop/blob/master/screenshots/keys.png?raw=true)

If you prefer not to specify keys in a `.rprofile` (especially if you are on a public computer/cluster/cloud server), you can specify both keys in the `dropbox_auth()` function directly (more below). <em>Note that once you have authorized an app, there is no need to call this function again.</em> You can just use your saved credential file to access your Dropbox. If for any reason, the file becomes compromised, just revoke access from your [list of authorized apps.](https://www2.dropbox.com/account#applications)

### Authorizing your app
<pre><code>
library(rDrop)

&#35; If you have Dropbox keys in your .rprofile, simply run:
 dropbox_credentials &lt;- dropbox_auth()

&#35; Otherwise:
 dropbox_credentials &lt;- dropbox_auth("Your_consumer_key", "Your_consumer_secret")
</code></pre>


If you entered valid keys, you will be directed to a secure Dropbox page on your browser asking you to authorize the app you just created. Click authorize to add this to your approved app list and return to R. This is a one time authorization. Once you have completed these steps, return to `R` and press enter (Ignore <em>When complete, record the PIN given to you and provide it here</em>). There is no need to run `dropbox_auth()` for each subsequent run. Simply save your credentials file to disk and load as needed:

<pre><code>
 save(dropbox_credentials, file="/path/to/my_dropbox_credentials.rdata")
</code></pre>

Credentials will remain valid until you revoke them from your [Dropbox Apps page](https://www2.dropbox.com/developers/apps) by clicking the x next to your App's name.

# Quick User Guide
This package essentially provides standard Dropbox file operation functions (create/copy/move/restore/share) from within `R`.

To load a previously validated Dropbox credential file:
<pre><code>
load('/path/to/my_dropbox_credentials.rdata')
&#35; or once again run,
dropbox_credentials &lt;- dropbox_auth('Your_consumer_key', 'Your_consumer_secret')
</code></pre>

### Summary of your Dropbox Account
<pre><code>
dropbox_acc_info(dropbox_credentials)
&#35; will return a list with your display name, email, quota, referral URL, and country.
</code></pre>

### Directory listing
<pre><code>
dropbox_dir(dropbox_credentials)
&#35; To list files and folders in your Dropbox root.

dropbox_dir(dropbox_credentials, verbose = TRUE)
&#35; for a complete listing (filename, revision, thumb, bytes, modified, path, and is_dir) with detailed information.


dropbox_dir(dropbox_credentials, path = 'folder_name')
&#35; To see contents of a specified path.

dropbox_dir(dropbox_credentials, path = 'folder_name', verbose = TRUE)
&#35; For verbose content listing for a specified path (relative to Dropbox root).
</code></pre>


### Download files from your Dropbox account to R
<pre><code>
<strong>Reading text files</strong>
file &lt;- dropbox_get(dropbox_credentials, 'my_data.csv')
data &lt;- read.csv(textConnection(file))
<br>
<strong>Reading images</strong>
...
</code></pre>

### Upload R objects to your Dropbox
<pre><code>
&#35; Function is not working correctly at the moment.
dropbox_save(dropbox_credentials, r_objects, file="filename")
</code></pre>

### Moving files within Dropobx
<pre><code>
dropbox_move(dropbox_credentials, from_path, to_path)
&#35; from_path can be a folder or file. to_path has to be a folder.
</code></pre>

### Copying files within Dropbox
<pre><code>
dropbox_copy(dropbox_credentials, from_path, to_path)
&#35; To overwrite existing file/folder in destination, add overwrite = TRUE.
</code></pre>

### Creating a public share for any Dropbox file or folder
<pre><code>
dropbox_share(dropbox_credentials, file)
&#35; File/folder to share. Returns share URL with expiration information.
&#35; Link goes directly to files. Folder are automatically zipped up.
</code></pre>

