1. Make sure both machines are on the same local network. In the office both devices need to be on the `SDC-BYOD` network. You may need to contact Help Desk for access.
2. Use an extended version of the `runserver` command to server the Django app: `./manage.py runserver 0.0.0.0:8000`
3. Determine your computer's ip with `ifconfig`. Look for the IP on `en0`. There will be several IPs, your IP will be labeled as `inet`
4. Manually enter `<your_ip>:8000` in any device to access your local server.
5. You can link iOS devices to safari in order to access developer tools for your device. 