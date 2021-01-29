---
title: Start Chrome With Flags (Mac)
excerpt: >-
  When using the Sinch JavaScript SDK locally in Chrome, it is necessary to
  start Chrome with the flag. Learn how to do it with our simple step-by-step
  guide.
---
When using the [Sinch JavaScript SDK](doc:voice-for-js) locally in Chrome, it is necessary to start Chrome with the flag `--allow-file-access-from-files`.

 1. Find the path to Chrome on your computer by opening chrome://version. Look for **Executable Path**. Copy that path.
 2. Quit Chrome\! Don’t just close your open tabs, but actually **quit\!** 
 3. From your terminal, run `$ YOUR_EXECUTABLE_PATH --allow-file-access-from-files`
 4. Chrome should launch now. Navigate back to chrome://version to see that the flag has been added properly 
   ![example.png](images\9069eea-example.png)
 5. To quit, press **Ctrl-C** in the terminal tab that is running Chrome.

