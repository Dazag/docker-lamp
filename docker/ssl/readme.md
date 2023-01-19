## How to configure docker and your browser to have a secure connection with docker and staging

* [How to configure docker](#docker)
* [How to configure staging](#staging)

<a name="docker">
### How to generate your own keys for a secure connection _"https"_ with docker
</a>

To install `certutil`:

```
sudo apt install libnss3-tools
```

Now let's build `mkcert` locally with `Go`:

```
git clone https://github.com/FiloSottile/mkcert && cd mkcert
docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.18.2 go build -ldflags "-X main.Version=$(git describe --tags)"
sudo mv mkcert /usr/local/bin
cd .. && sudo rm -rf mkcert
```

We can now generate our first local CA certificate with the command:

`mkcert -install`

The above command will generate your new certification, without you needing to input a single bit of information. The certificate will be saved into the local store, which you can locate with the command:

`mkcert -CAROOT`

Next, we’ll generate a certificate for our local websites, www.docker.minderest.com and minderest.test with the commands:

```
cd minderest-web-app/docker/ssl
mkcert www.docker.minderest.com minderest.test
# Rename the files to match the configuration
mv whatever-name-cert.pem local-cert.pem
mv whatever-name-key.pem local-key.pem
```

Build docker again and you should be done!
`docker-compose up --build`

<a name="staging">
### How to configure a secure connection _"https"_ against staging (_staigng.local.minderest.com_) 
</a>

If you are using Chrome or Vivaldi:

* Go to the address bar of the web browser and paste this `chrome://settings/certificates`.
* Click on Authorities.
* Click on Import.
* Select the certificate in this folder named: `staging-minderest-CA.pem`.
* Click open and check all the options listed.
* Click OK.
* Go to [staging](https://staigng.local.minderest.com) and verify you have a secure connection.
* DONE!

If you are using Firefox:

TODO by someone with firefox.
