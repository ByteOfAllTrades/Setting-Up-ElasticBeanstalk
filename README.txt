Elastic Beanstalk is a wonderful solution to a multitude of problems, that said, there are some issues. The first of these issues being that the *proper* documentation is often hard to find and very vague, especially when setting up .conf and environments.

This is by no means supposed to be a comprehensive guide to EBS(Read the real docs for that: https://docs.aws.amazon.com/elastic-beanstalk/index.html), just some quick solutions to relatively simple tasks.

1. Where do my .conf files go?
This is easy, create a folder in the root directory of the project, and title it ".ebextensions" all .conf files should go here. Also, it's important to remember that .conf files are loaded in alphabetical order.

2. How do I execute commands on container startup?
This involves the commands directive. and it looks like this:
commands:
    command block:
        command: |
            mkdir /var/log/pingu
            mkdir /var/log/pingu/nootnoot
            chown -R webapp:webapp /var/log/pingu
This setup is shown as a contiguous block of commands, you can add as many as you like. However, there is another way if you prefer a more verbose look. It looks like this:
container_commands:
	Begin the crusade:
 		command: mkdir nootnoot
This approach sections out each block of commands based on the names you give them.

As an important side note, commands will fail if the directory they are targeting does not exist yet, I'll tell you how to fix that later on.

3. How do I create files?
Similarly to commands, this involves the files directive, and it looks like this:
files:
  "/etc/pingu.php" :
    mode: "000644"
    owner: root
    group: root
    content: |
      <?php
      phpinfo();
Let's break it down. Directly after the files directive, we have the path, this is where your file is going to be created(if it exists). Then the mode setting determines the permissions for your file. Next, we have the owner and group settings, which are self-explanatory to anyone familiar with Linux*. Finally, we have the content followed by a pipe which denotes a block of text for the content, this is where you put your code or whatever.

4. How do I pre-create the environment so my commands don't fail?
Good question! In Elastic Beanstalk you can do this by leveraging the .plaform file (created in the same directory as .ebextensions). After creating this file building out and file structure inside of it will pre-initialize that file structure, even before adding the other parts of the system. for example:
.platform
	etc
		noot.php
	nginx
		nginx.conf
These will all be created before the .conf files are run.

Please note that you should initialize an empty file with the same name inside of a .platform directory if you are planning on creating it with the files directive.

That's all I have, for now, hopefully, this answers some questions regarding EBS.