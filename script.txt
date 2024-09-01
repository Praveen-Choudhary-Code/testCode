#yum repository with mount the redhat iso dvd 
#!/bin/bash

# Define the directory path and device
DIR="/qqq"
DEVICE="/dev/sr0"
REPO_FILE="/etc/yum.repos.d/myrepo.repo"

# Task 1: Check if the directory exists
if [ -d "$DIR" ]; then
  echo "Directory $DIR already exists."
else
  # Create the directory
  mkdir "$DIR"
  echo "Directory $DIR created successfully."
fi

# Task 2: Mount /dev/sr0 to /qqq
# Check if the device exists
if [ -b "$DEVICE" ]; then
  # Mount the device to the directory
  mount "$DEVICE" "$DIR"
  if [ $? -eq 0 ]; then
    echo "Device $DEVICE mounted to $DIR successfully."
  else
    echo "Failed to mount $DEVICE to $DIR. Please check the device and directory."
  fi
else
  echo "Device $DEVICE does not exist. Please check the device path."
fi

# Task 3: Create a repository file in /etc/yum.repos.d
if [ -f "$REPO_FILE" ]; then
  echo "Repository file $REPO_FILE already exists."
else
  # Create the repository file
  cat <<EOL | sudo tee "$REPO_FILE" > /dev/null

[AppStrem]
name= MyLocal-AppStrem-repo
baseurl=file:///qqq/AppStream
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[BaseOS]
name= myBaseos-repo
baseurl=file:///qqq/BaseOS
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

EOL
  echo "Repository file $REPO_FILE created successfully."
fi


# Script to install EPEL release using yum

echo "Installing EPEL release..."
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-$(rpm -E '%{rhel}').noarch.rpm