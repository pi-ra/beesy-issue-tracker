# Beesy: Troubleshooting Common Issues

If the issue you're experiencing is not listed here, please report it on our bug tracker [here](https://github.com/pi-ra/beesy-issue-tracker/issues/new?labels=bug&template=bug_report.yml&title=).

# Table of Contents
- [Beesy: Troubleshooting Common Issues](#Beesy-Troubleshooting-Common-Issues)
    - [Recording download failed](#Recording-download-failed)
    - [Cannot play recording or only plays for few seconds](#Cannot-play-recording-or-only-plays-for-few-seconds)
    - [Recording didn't start within 5 seconds](#Recording-didnt-start-within-5-seconds)


### Recording download failed

Large recordings may fail to download due to limited browser resources, follow these steps:

1. Right-click anywhere and click **Inspect**
   ![right-click and inspect](../media/trble-inpct.png)
2. Click on **Console** tab
   ![click console tab](../media/trble-cnsl.png)
3. Replace **YOUR_FILENAME_HERE** with the recording name that you are looking for like (_beesy_recording-2025_07_16_12_49_44-gwf_cucm_ynx.webm_) in the command below

   ```
    (async function downloadFile(fileName) {
      const opfsHandle = await navigator.storage.getDirectory();
      const recDir = await opfsHandle.getDirectoryHandle('beesy_recordings');
      const fileToDownloadHandle = await recDir.getFileHandle(fileName);
      const fileToDownload = await fileToDownloadHandle.getFile();
      const blb = new Blob([fileToDownload], {
        type: 'video/webm',
      });
      chrome.downloads.download({
        url: URL.createObjectURL(blb),
        filename: fileName,
      });

      const ccDir = await opfsHandle.getDirectoryHandle('beesy_cc');
      try {
        const ccFileHandle = await ccDir.getFileHandle(fileName + '.txt');
        const ccFile = await ccFileHandle.getFile();
        const ccBlob = new Blob([ccFile], {
          type: 'text/plain',
        });
        chrome.downloads.download({
          url: URL.createObjectURL(ccBlob),
          filename: ccFile.name,
        });
      } catch (error) {
        if (error.name === 'NotFoundError') {
          console.log('No CC file found for', fileName);
          return
        }
        // Handle other errors if necessary
        // For example, you might want to log the error or show a message to the user
        console.error('Error retrieving CC file:', error);
      }
    })('YOUR_FILENAME_HERE')
   ```

4. Paste the modified command in **Console** and press _Enter_ to download the file

### Cannot play recording or only plays for few seconds
If the recorded video plays for only few seconds and then just ends, try these things:
- try playing in VLC media player, recordings are in .webm format which isn't supported by default media players, but VLC should be able to play them
- if you don't want to use VLC, then the video needs to be patched, that can be done in settings as shown below
  - <img width="2242" height="1202" alt="image" src="https://github.com/user-attachments/assets/e1e45cf6-71cf-4a76-b743-ef0b619e6bb1" />


### Recording didn't start within 5 seconds

If you get the notification 'Recording might have failed to start...' then follow the steps below to fix it.

1. Navigate to [Beesy extension (chrome://extensions/?id=eabicnldgjknbifdgmnieblkbnggfnde)](chrome://extensions/?id=eabicnldgjknbifdgmnieblkbnggfnde) details page
2. Disable and then enable the extension
   ![toggle on-off-on](../media/trble-extn-toggle.png)
3. You MUST refresh the Google Meet page after this, otherwise you might still face some issue.
4. If the error persists, restart your browser which would fix it.
