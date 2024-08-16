General Explanation:
1. The name of the Actions will be "Deploy to EC2"
2. Every time there is a change, push to the branch - "main"
3. Run the latest version of ubuntu (most recent)
4. Drag all the files from my repository into the virtual machine we created
5. Installs the "open ssh" package
6. Open a new folder called .ssh, then write the key into a file inside the folder and at the same time the name of the file will be the name of our key, in addition bring the relevant permissions to the key in order to connect via ssh
7. Copy the index.html file from my Repository, to my ec2 machine, using the key, and finally save the file in: /home/ubuntu inside our virtual machine.
* scp -o StrictHostKeyChecking=no -i = allows files to be copied between different computers/systems given that the key is known to us, and in addition without checking the remote host
