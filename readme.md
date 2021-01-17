# Canvas2Cloud Sync Tool

Easy-to-use, versatile, platform-independent program that downloads course files from Canvas and syncs them to your cloud storage of choice. Gracefully handles incompatible filenames and preserves deleted/outdated files in a separate folder.
Handles content stored in Modules as well.
Works best when run from an always-on, internet-connected machine such as a Raspberry Pi.

## Prerequisites
This tool requires [rclone](https://rclone.org/downloads/) along with Python 3.7 or later. Install
Python dependencies with pip:
```bash
$ pip3 install -r requirements.txt
```

## Quick start
1. Download or clone the repo.
2. Set up a new remote in rclone:
   ```
   $ rclone config
   ...
   n/r/c/s/q> n
   ```
   Remember the remote name you set, as this will be used later.
3. In a web browser, login to Canvas and head to Account > Settings. Under Approved Integrations, click New Access Token. Type a purpose and click Generate Token.
4. Copy the access token that appears.
5. Open the `settings.yaml.starter` repo file in a text editor.
6. In the `tokens` object, paste your access token in place of `<access_token>`.
7. Next to `timezone`, set your local timezone that you would like to appear in file version names. (See [here](https://stackoverflow.com/questions/13866926/is-there-a-list-of-pytz-timezones) for a list of acceptable options.)
8. Next to `base_url`, change the domain from ```umich.instructure.com``` to whatever domain your school's Canvas uses.
9. (optional) If you prefer a different time format to be displayed in file version names, set it at `time_fmt`. See [this page](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior) for more details.
10. Set up the courses for which you would like your files synced. Go back to your web browser and visit the homepage for the course in Canvas. In the url bar, you should see something like this:
    ```
    https://<canvas-domain>/courses/123456
    ```
11. Copy the number at the end. This is the course id.
12. In the settings file, edit the `id` of one of the courses to this value. Make sure the `access_token` key refers to the name in front of the actual token you pasted in step 6.
13. If you want to sync content from the Modules page instead of the Files page, add `modules: true` to the corresponding course in the settings file.
13. For the `rclone` key, change the drive to the remote name you set in step 2, and change the path to wherever you want your course files to appear relative to your cloud storage.
14. Repeat steps 10-13 for each course. You can have as many `courses`, `tokens`, or `rclone` entries you want.
15. Delete any default entries you aren't using.
15. Finally, rename the `settings.yaml.starter` file to `settings.yaml`:
    ```
    $ mv settings.yaml.starter settings.yaml
    ```
16. Run the canvassync.py file to test it. (Use ```./canvassync.py -h``` to see available options.)
17. Make a cronjob using ```crontab -e``` to have your files synced automatically on a specified interval and receive email notifications on error. For example, to sync your files every 10 minutes, add the following to the file:
    ```
    MAILTO:<your_email>
    */10 * * * * /usr/bin/flock -n /tmp/canvassync.lockfile <path_to_canvassync.py>
    ```
