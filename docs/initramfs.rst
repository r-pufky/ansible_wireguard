.. _service-wireguard-initramfs:

wireguard-initramfs
###################
Enable wireguard while booting.

This enables the used of Dropbear and related unlock utilities over a wireguard
network before a system has booted.

Install
*******
Install :ref:`service-wireguard` first, then install the latest package from
https://github.com/r-pufky/wireguard-initramfs:

.. code-block:: bash
  :caption: Install wireguard-initramfs

  wget https://github.com/r-pufky/wireguard-initramfs/archive/refs/tags/2025-03-02.tar.gz
  tar xvf 2025-03-02.tar.gz
  cd wireguard-initramfs-2025-03-02
  make install

Configure
*********
.. code-block:: bash
  :caption: **0600 root root** /etc/wireguard/initramfs.conf

  [Interface]
  Address = 172.31.255.10/32
  PrivateKey = XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

  [Peer]
  PublicKey = gyW39I9bAiOBXyhL8LWw9qwiTZgMmtAbsWtLUv8uKTc=
  AllowedIPs = 172.31.255.254/32
  PresharedKey = XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  PersistentKeepalive = 25
  Endpoint = {SERVER INTERFACE IP}:51820

.. code-block:: bash
  :caption: **0644 root root** /etc/wireguard/initramfs

  ADAPTER=/etc/wireguard/initramfs.conf

  # Enable wg-quick for adapter management?
  ENABLE_QUICK=

  # URL to send a web request to set the local datetime.
  #
  # Raspberry Pi's should enable this feature for wireguard-initramfs to work.
  #
  # Skipped if blank.
  DATETIME_URL=

  # Persist (do not down) interface after initramfs exits? Any value enables.
  PERSISTENT=

  # Enable debug logging (will expose key material)? Any value enables.
  DEBUG=

.. note::
  Most systems do not encrypt ``/boot`` so private key material is exposed and
  considered **compromised/untrusted**. Boot wireguard network should be
  **different** & untrusted, versus the network used after booting. Always
  restrict ports and access on the wireguard server.

.. code-block:: bash
  :caption: Add wireguard to initramfs.

  update-initramfs -u
  update-grub
  reboot

Dropbear Remote Unlock
**********************
Unlock an encrypted root filesystem remotely on boot over wireguard.

Ensure that both Dropbear and :ref:`service-wireguard`,
:ref:`service-wireguard-initramfs` are setup and working correctly.
Then set dropbear to only listen over wireguard network:

.. code-block:: bash
  :caption: **0644 root root** /etc/dropbear-initramfs/config

  DROPBEAR_OPTIONS='... -p 172.31.255.10:22 ...'

.. code-block:: bash
  :caption: Update dropbear config in initramfs.

  update-initramfs -u
  update-grub
  reboot

* The boot wireguard network should be separate from your normal wireguard
  network. Protect the server endpoint and restrict all ports not needed.
* The boot and running wireguard networks should have different keys.
* Set UFW on the host as well for further protection.
