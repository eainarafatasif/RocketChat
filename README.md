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

To install Rocket.Chat with Nginx, MySQL, and an SSL certificate on Ubuntu 22.04, follow these steps:

### 1. **Update the System**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

### 2. **Install Required Dependencies**
   ```bash
   sudo apt install -y curl build-essential software-properties-common
   ```

### 3. **Install MongoDB**
   Rocket.Chat uses MongoDB as its primary database.
   ```bash
   curl -fsSL https://pgp.mongodb.com/server-6.0.asc | sudo tee /usr/share/keyrings/mongodb-server-6.0.gpg > /dev/null
   echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
   sudo apt update
   sudo apt install -y mongodb-org
   sudo systemctl enable mongod --now
   ```

### 4. **Install Node.js**
   Rocket.Chat requires Node.js.
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
   sudo apt install -y nodejs
   ```

### 5. **Install Rocket.Chat**
   ```bash
   sudo mkdir -p /opt/Rocket.Chat
   sudo chown -R $USER:$USER /opt/Rocket.Chat
   cd /opt/Rocket.Chat
   curl -L https://releases.rocket.chat/latest/download -o rocket.chat.tgz
   tar -xvzf rocket.chat.tgz
   rm rocket.chat.tgz
   cd bundle/programs/server
   npm install
   cd ../../..
   ```

### 6. **Create a Systemd Service for Rocket.Chat**
   ```bash
   sudo tee /lib/systemd/system/rocketchat.service << EOF
   [Unit]
   Description=The Rocket.Chat Server
   After=network.target remote-fs.target nss-lookup.target mongod.target

   [Service]
   ExecStart=/usr/bin/node /opt/Rocket.Chat/main.js
   StandardOutput=syslog
   StandardError=syslog
   SyslogIdentifier=rocketchat
   User=rocketchat
   Environment=MONGO_URL=mongodb://localhost:27017/rocketchat
   Environment=ROOT_URL=http://localhost:3000
   Environment=PORT=3000
   Restart=always
   RestartSec=10
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

   Then reload the systemd daemon and start Rocket.Chat:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable rocketchat --now
   ```

### 7. **Install and Configure Nginx**
   ```bash
   sudo apt install -y nginx
   ```

   Create a new Nginx configuration file for Rocket.Chat:
   ```bash
   sudo tee /etc/nginx/sites-available/rocketchat << EOF
   server {
       listen 80;
       server_name your_domain.com;

       location / {
           proxy_pass http://localhost:3000/;
           proxy_http_version 1.1;
           proxy_set_header Upgrade \$http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header Host \$host;
           proxy_set_header X-Real-IP \$remote_addr;
           proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto \$scheme;
           proxy_set_header X-NginX-Proxy true;
           proxy_redirect off;
       }
   }
   EOF
   ```

   Enable the configuration and restart Nginx:
   ```bash
   sudo ln -s /etc/nginx/sites-available/rocketchat /etc/nginx/sites-enabled/rocketchat
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### 8. **Set Up MySQL**
   Install MySQL and secure the installation:
   ```bash
   sudo apt install -y mysql-server
   sudo mysql_secure_installation
   ```

   Create a MySQL database and user for Rocket.Chat:
   ```bash
   sudo mysql -u root -p
   CREATE DATABASE rocketchat;
   CREATE USER 'rocketchat'@'localhost' IDENTIFIED BY 'yourpassword';
   GRANT ALL PRIVILEGES ON rocketchat.* TO 'rocketchat'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

### 9. **Install Certbot for SSL**
   ```bash
   sudo apt install -y certbot python3-certbot-nginx
   sudo certbot --nginx -d your_domain.com
   ```

   Follow the prompts to obtain and configure the SSL certificate.

### 10. **Final Configuration**
   Update the Rocket.Chat systemd service to include MySQL:
   ```bash
   sudo nano /lib/systemd/system/rocketchat.service
   ```

   Modify the `Environment=MONGO_URL` line if you want to point to MySQL. Since Rocket.Chat uses MongoDB by default, this step may not be necessary unless you intend to manage other related databases with MySQL.

   Reload systemd:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart rocketchat
   ```

Rocket.Chat should now be accessible via `https://your_domain.com`.

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
