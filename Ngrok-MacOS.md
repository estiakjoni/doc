# Ngrok

Ngrok exposes local servers behind NATs and firewalls to the public internet over secure tunnels. Public URLs for exposing your local web server.



## Install ngrok on macOS

```bash
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-darwin-amd64.zip && \
unzip /path/to/ngrok.zip && \
./ngrok authtoken 1Oybx3UMYPTdUDiXaD26TCg3WfI_87svF5rLEEJVFsAJ35M1k && \
mv ngrok /usr/local/bin/ngrok && \
chmod +x /usr/local/bin/ngrok
```



Run ngrok command on the command prompt. This will provide you ngrok version details.

```bash
ngrok -V
#ngrok version 2.3.35
```

