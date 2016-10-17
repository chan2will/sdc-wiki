# Access Django server from a device locally

1. Make sure both machines are on the same local network.
2. Use an extended version of the `runserver` command to server the Django app: `./manage.py runserver 0.0.0.0:8000`
3. Determine your computer's ip with `ifconfig`. Look for the IP on `en0`.
4. Manually enter `<your_ip>:8000` in any device to access your local server.
5. You can link iOS devices to safari in order to access developer tools for your device. 