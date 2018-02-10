# Adding a project and SSH keys

## Choose a good name

Your project should have a name that tells what kind of a project it is and it should help to group your servers into logical structure.

Some examples:  
- Client Websites
- Open Source
- Development
- Databases
- Private

## Create the project

Go to the project management page in your Hetzner account, click the "Add Project" button, type in your project name and hit [Enter].

- [Project Management](https://console.hetzner.cloud/projects)

## Add your SSH key

To be able to securely login into the new servers you create you should add your SSH keys to the project.  
If you don't connect SSH keys the server will be accessible with a generated password and bots or hackers can try to brute-force your root account.

Go to the SSH Key Management page by using the navigation on the left side, after selecting your new project.

<img src="./images/hetzner-cloud-access.jpg" alt="SSH Key Management page" width="200">

Add your public SSH key of the user and machine you use to connect to your new server.

<img src="./images/hetzner-cloud-access-add-key.jpg" alt="SSH Key Dialog" width="200">


<div style="border-top: 2px solid #a0a0a0; margin-top: 2.5rem; padding-top: 1rem;">
    <div style="float: left">
        &laquo;
        <a href="./registering-a-free-hetzner-account.md">
            Registering a free Hetzner account
        </a>
    </div>
    <div style="float: right;">
        <a href="./creating-a-small-server.md">
            Creating a small server
        </a>
        &raquo;
    </div>
</div>