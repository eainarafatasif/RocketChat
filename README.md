<p align="center"> 
  <img  src="https://github.com/RocketChat/Rocket.Chat.Artwork/raw/master/Logos/2020/png/logo-horizontal-red.png" data-canonical-src="https://github.com/RocketChat/Rocket.Chat.Artwork/raw/master/Logos/2020/png/logo-horizontal-red.png" width="400" />
</p>

<h1 align="center">
  The ultimate Free Open Source Solution for team communications
</h1>

<p align="center">
  <img src="https://img.shields.io/github/v/release/RocketChat/Rocket.Chat?label=version">
  <img src="https://img.shields.io/github/actions/workflow/status/RocketChat/Rocket.Chat/build_and_test.yml">
  <img src="https://img.shields.io/badge/license-MIT-green">
  <img alt="Codecov branch" src="https://img.shields.io/codecov/c/github/RocketChat/Rocket.Chat/develop">
</p>



[Rocket.Chat](https://rocket.chat) is an open-source fully customizable communications platform developed in JavaScript for organizations with high standards of data protection.

We are the ultimate **Free Open Source Solution** for team communications, enabling real-time conversations between colleagues, with other companies or with your customers, regardless of how they connect with you. The result is an increase in productivity and customer satisfaction rates.

Every day, tens of millions of users in over 150 countries and in organizations such as Deutsche Bahn, The US Navy, and Credit Suisse trust Rocket.Chat to keep their communications completely private and secure.

# 🚀 Product Offerings - Self Hosted and Cloud

Rocket.Chat has four key product offerings: 

💬 **Team collaboration** - A single point for secure internal and cross-company collaboration.

🎯 **Omnichannel customer service** - Engage in seamless interactions with customers regardless of how they connect with you.

🦾 **Chat engine** - Create customized messaging experiences within your mobile or web app.

🤖 **Marketplace** - Choose a wide range of apps and native integrations that help your business communicate more effectively.

<img src="https://global-uploads.webflow.com/611a19b9853b7414a0f6b3f6/6319c72bbd8af5c8c22efab6_heroImage.webp" data-canonical-src="https://global-uploads.webflow.com/611a19b9853b7414a0f6b3f6/6319c72bbd8af5c8c22efab6_heroImage.webp"  />

# ☁️ Cloud Hosted Rocket.Chat

Send your first message in minutes.

Free for 30 days. Afterward, choose between continuing to host on our secure cloud or migrating to your private cloud, data center, or even air-gapped environment.

[Start your cloud hosted trial now](https://rocket.chat/trial-saas)

# 🛠️ Local development

##  Prerequisites

You can follow these instructions to setup a dev environment:

- Install **Node 14.x (LTS)** either [manually](https://nodejs.org/dist/latest-v14.x/) or using a tool like [nvm](https://github.com/creationix/nvm) or [volta](https://volta.sh/) (recommended)
- Install **Meteor** ([version here](apps/meteor/.meteor/release)): https://www.meteor.com/developers/install
- Install **yarn**: https://yarnpkg.com/getting-started/install
- Clone this repo: `git clone https://github.com/RocketChat/Rocket.Chat.git`
- Run `yarn` to install dependencies

**Starting Rocket.Chat:**

Installing Rocket.Chat on Ubuntu 22.04 involves setting up MongoDB, installing Rocket.Chat, and configuring it as a service. Below are the steps to guide you through the process:

### Step 1: Update the System
First, update the system packages to ensure everything is up to date.

```bash
sudo apt-get update
sudo apt-get upgrade -y
```

### Step 2: Install MongoDB
Rocket.Chat requires MongoDB as its database. Install MongoDB using the following commands:

1. Import the public key used by the package management system:

   ```bash
   wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
   ```

2. Create a list file for MongoDB:

   ```bash
   echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
   ```

3. Reload the package database:

   ```bash
   sudo apt-get update
   ```

4. Install MongoDB:

   ```bash
   sudo apt-get install -y mongodb-org
   ```

5. Start and enable MongoDB:

   ```bash
   sudo systemctl start mongod
   sudo systemctl enable mongod
   ```

### Step 3: Install Node.js
Rocket.Chat requires Node.js. Install the latest LTS version:

1. Install the NodeSource Node.js 14.x repository:

   ```bash
   curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
   ```

2. Install Node.js:

   ```bash
   sudo apt-get install -y nodejs
   ```

### Step 4: Install Rocket.Chat
Now, download and install Rocket.Chat:

1. Create a directory for Rocket.Chat:

   ```bash
   sudo mkdir -p /opt/Rocket.Chat
   ```

2. Navigate to the directory:

   ```bash
   cd /opt/Rocket.Chat
   ```

3. Download the latest Rocket.Chat tarball:

   ```bash
   sudo curl -L https://releases.rocket.chat/latest/download -o rocket.chat.tgz
   ```

4. Extract the tarball:

   ```bash
   sudo tar -xvzf rocket.chat.tgz
   sudo mv bundle/* /opt/Rocket.Chat
   ```

5. Install the necessary dependencies:

   ```bash
   cd /opt/Rocket.Chat/programs/server
   sudo npm install
   ```

### Step 5: Configure Rocket.Chat
Set the environment variables and start Rocket.Chat:

1. Set environment variables:

   ```bash
   export MONGO_URL=mongodb://localhost:27017/rocketchat
   export ROOT_URL=http://your-domain.com:3000
   export PORT=3000
   ```

2. Start Rocket.Chat:

   ```bash
   cd /opt/Rocket.Chat
   sudo node main.js
   ```

### Step 6: Set Up Rocket.Chat as a Service
To ensure Rocket.Chat runs automatically on startup, set it up as a systemd service:

1. Create a systemd service file:

   ```bash
   sudo nano /etc/systemd/system/rocketchat.service
   ```

2. Add the following content:

   ```ini
   [Unit]
   Description=Rocket.Chat server
   After=network.target remote-fs.target nss-lookup.target mongod.target

   [Service]
   ExecStart=/usr/bin/node /opt/Rocket.Chat/main.js
   StandardOutput=syslog
   StandardError=syslog
   SyslogIdentifier=rocketchat
   User=root
   Environment=MONGO_URL=mongodb://localhost:27017/rocketchat
   Environment=ROOT_URL=http://your-domain.com:3000
   Environment=PORT=3000
   Environment=NODE_ENV=production
   WorkingDirectory=/opt/Rocket.Chat

   [Install]
   WantedBy=multi-user.target
   ```

3. Reload the systemd daemon to apply the new service file:

   ```bash
   sudo systemctl daemon-reload
   ```

4. Start and enable Rocket.Chat:

   ```bash
   sudo systemctl start rocketchat
   sudo systemctl enable rocketchat
   ```

### Step 7: Access Rocket.Chat
Rocket.Chat should now be accessible via your browser at `http://your-domain.com:3000`. Replace `your-domain.com` with your server's domain name or IP address.

### Optional: Set Up Nginx as a Reverse Proxy
If you want to run Rocket.Chat behind an Nginx reverse proxy (for SSL, etc.), you can set it up as follows:

1. Install Nginx:

   ```bash
   sudo apt-get install nginx -y
   ```

2. Configure Nginx:

   ```bash
   sudo nano /etc/nginx/sites-available/rocketchat
   ```

3. Add the following configuration:

   ```nginx
   server {
       listen 80;
       server_name your-domain.com;

       location / {
           proxy_pass http://localhost:3000/;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forward-Proto http;
           proxy_set_header X-Nginx-Proxy true;
           proxy_redirect off;
       }
   }
   ```

4. Enable the configuration and restart Nginx:

   ```bash
   sudo ln -s /etc/nginx/sites-available/rocketchat /etc/nginx/sites-enabled/rocketchat
   sudo systemctl restart nginx
   ```

Rocket.Chat should now be running behind Nginx, and accessible via `http://your-domain.com`. For SSL, you can use Let's Encrypt to secure your domain.

That's it! Rocket.Chat should now be fully operational on your Ubuntu 22.04 server.

After initialized, you can access the server at http://localhost:3000

More details at: [Developer Docs](https://developer.rocket.chat/v1/docs/server-environment-setup)
PS: For Windows you MUST use WSL2 and have +12Gb RAM


# Gitpod Setup

1. Click the button below to open this project in Gitpod.

2. This will open a fully configured workspace in your browser with all the necessary dependencies already installed.

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/RocketChat/Rocket.Chat)

**Starting Rocket.Chat in microservices mode:**

```bash
yarn turbo run ms
```

After initialized, you can access the server at http://localhost:4000

> ⚠️ Check more detailed information in the [Rocket.Chat Environment Setup](https://developer.rocket.chat/rocket.chat/rocket-chat-environment-setup) guide

# 💻 Installation

Please see the [requirements documentation](https://docs.rocket.chat/deploy/installing-client-apps/minimum-requirements-for-using-rocket.chat) for system requirements and more information about supported operating systems.
Please refer to [Install Rocket.Chat](https://rocket.chat/install) to install your Rocket.Chat instance.

# 📱 Mobile Apps

In addition to the web interface, you can also download Rocket.Chat clients for:


[![Rocket.Chat on Apple App Store](https://user-images.githubusercontent.com/551004/29770691-a2082ff4-8bc6-11e7-89a6-964cd405ea8e.png)](https://itunes.apple.com/us/app/rocket-chat/id1148741252?mt=8) [![Rocket.Chat on Google Play](https://user-images.githubusercontent.com/551004/29770692-a20975c6-8bc6-11e7-8ab0-1cde275496e0.png)](https://play.google.com/store/apps/details?id=chat.rocket.android) [![](https://user-images.githubusercontent.com/551004/48210349-50649480-e35e-11e8-97d9-74a4331faf3a.png)](https://f-droid.org/en/packages/chat.rocket.android)

You can also contribute to the Mobile open source code in [Rocket.Chat.ReactNative](https://github.com/RocketChat/Rocket.Chat.ReactNative) and check it out its [documentation](https://developer.rocket.chat/mobile-app/mobile-app-environment-setup) 

# 🧩 Apps Engine for Rocket.Chat

You can develop your own app that can be integrated with Rocket.Chat. We provide an [Open Source Apps Engine framework](https://developer.rocket.chat/apps-engine/getting-started) increasing the world of possibilities of integrations around the Rocket.Chat ecosystem

# 📚 Learn More

- [Product Documentation](https://docs.rocket.chat)
- [Developer Documentation](https://developer.rocket.chat)
- [API Documentation](https://developer.rocket.chat/reference/api)
- [Apps Engine Development](https://developer.rocket.chat/apps-engine/rocket.chat-apps-and-apps-engine)
- [See who's using Rocket.Chat](https://www.rocket.chat/customers)

# 🆕 Feature Request

[Rocket.Chat/feature-requests](https://github.com/RocketChat/feature-requests) is used to track Rocket.Chat feature requests and discussions. Click [here](https://github.com/RocketChat/feature-requests/issues/new?template=feature_request.md) to open a new feature request. [Feature Request Forums](https://forums.rocket.chat/c/feature-requests/8) stores the historical archives of old feature requests (up to 2018).

# 🤝 Community

Join thousands of members worldwide in our [community server](https://open.rocket.chat).
Join [#support](https://open.rocket.chat/channel/support) and [#general](https://open.rocket.chat/channel/general) for help from the community.


# 👥 Contributions

Rocket.Chat is an open source project and we are very happy to accept community contributions. Please refer to the [How can I help?](https://developer.rocket.chat/contribute-to-rocket.chat/ways-to-contribute) page for more details.


## 💼 Become a Rocketeer

We're hiring developers, support people, and product managers all the time. Please check our [jobs page](https://rocket.chat/jobs).

## 🗞️ Get the Latest News

- [Blog](https://rocket.chat/blog)
- [Twitter](https://twitter.com/RocketChat)
- [Facebook](https://www.facebook.com/RocketChatApp)
- [LinkedIn](https://www.linkedin.com/company/rocket-chat)
- [Youtube](https://www.youtube.com/channel/UCin9nv7mUjoqrRiwrzS5UVQ)

## 🗒️ Credits

- Emoji provided graciously by [JoyPixels](https://www.joypixels.com).
