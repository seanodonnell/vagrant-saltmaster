Vagrantfile for running a basic salt master for development, plus instructions for connecting a vagrant minion

INSTRUCTIONS FOR USE:

Run vagrant up to start the salt master
connect to it with vagrant ssh
generate your master keys with:
	sudo salt-key --gen-keys=master
	sudo cp master.p* /vagrant/saltkeys/
	rm master.p*
now generate the keys for your minion
	sudo salt-key --gen-keys=hostname
	sudo cp hostname.p* /vagrant/saltkeys/
	rm hostname.p*

remove the comments from the following lines in the Vagrantfile, and make sure the key filenames are accurate

salt.master_key = 'saltkeys/master.pem'
#       salt.master_pub = 'saltkeys/master.pub'

        salt.seed_master = {
#               'minion' => 'saltkeys/minion.pub',
		        }


On your minion, set the keys in its vagrant file

        salt.minion_key = 'saltkeys/hostname.pem'
        salt.minion_pub = 'saltkeys/hostname.pub'

Make sure the minions hostname matches what you used when generated the key

  config.vm.hostname = "hostname"

Give the minion a config file

salt.minion_config = 'salt/hostname.conf'

and specify the ip of the saltmaster in it

master: 192.168.0.10

There is a sample vagrant minion Vagrantfile in this directory called minionVagrantfile

now stop and start your master (vagrant halt && vagrant up)

then check that the master has accepted the key

vagrant ssh
sudo -s
salt-key -L

your should see output like this:

Accepted Keys:
hostname
Unaccepted Keys:
Rejected Keys:


now start your minion with vagrant up (assuming its the first time it has been run, if not I would advise, stopping, starting and provisioning it)

if you have not defined any state yet, when the minion starts you will see output like the following:
	
	 
local:
----------
          ID: states
    Function: no.None
      Result: False
     Comment: No Top file or external nodes data matches found
     Changes:   

Summary
------------
Succeeded: 0
Failed:    1
------------
Total:     1

This is ok, its just reporting that no states have been found. salt is working and the minion is talking to the master, now you can definte your states and begin to provision your project
