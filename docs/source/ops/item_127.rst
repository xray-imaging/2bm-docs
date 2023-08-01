SSH key update
==============

To update the ssh key between beamline control and detector control machines::

	[user2bmb@arcturus] $ ssh-copy-id remote_username@server_ip_address

if ssh-copy-id gives an error::

	ERROR: No identities found

it meeans you need to generate a new ssh key first with::

	[user2bmb@arcturus] $ ssh-keygen -t rsa
