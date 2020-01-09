# wcm_io_devops.conga_ansible_controlhost

This role setups a host as a controlhost for the
[wcm.io DevOps Ansible Automation for AEM](http://devops.wcm.io/ansible-aem/).

The role installs the following tools
* JDK
* Maven
* Ansible (inlcuding required pip packages like cffi, jmespath, boto etc.)
* Terraform

When provided the role will deploy your AWS `credentials` and the
`.vault_pass file`.

The role also enables you to configure the environment in your .bashrc
if required.
* EDITOR
* ANSIBLE_VAULT_PASSWORD_FILE

For Maven the provisioning of `settings.yml` and the
`settings-security.xml` (optional) is supported.

Beside the required os packages for the tools the following packages
will be installed to allow fast monitoring and debugging:
* nano
* iftop
* htop
* iotop

## Requirements

This role requires Ansible 2.0 or higher.

## Role Variables

Available variables are listed below, along with default values.

    controlhost_dependency_maven: true

Enables / disables the maven role dependency.

    controlhost_dependency_java: true

Enables / disables the java role dependency.

    controlhost_dependency_epel: true

Enables / disables the epel role dependency.

    controlhost_dependency_terraform: true

Enables / disables the terraform role dependency.

    controlhost_user: "{{ ansible_env.SUDO_USER | default(ansible_env.USER) }}"

The user on the controlhost.

    controlhost_group: "{{ controlhost_user }}"

The users group on the controlhost.

    controlhost_user_home: "/home/{{ controlhost_user }}"

The home directory of the user.

    controlhost_maven_path: "{{ controlhost_user_home }}/.m2"

Path to the Maven directory.

    controlhost_maven_settings_path: "{{ controlhost_maven_path }}/settings.xml"

Path to Maven settings.xml.

    controlhost_maven_settings_security_path: "{{ controlhost_maven_path }}/settings-security.xml"

Path to Maven settings-security.xml.

    controlhost_maven_artifact_threads: 5

Threads used by maven when downloading artifacts. Increasing improves
download speed.

    #controlhost_maven_master_password:

When set, a Maven settings-security.xml will be created with the
encrypted master password and the credentials for the
controlhost_maven_repositories will be encrypted using the Maven master
password.

    controlhost_nodejs_npmrc_path: "{{ controlhost_user_home }}/.npmrc"

Path to the .npmrc.

    controlhost_nodejs_npmrc_always_auth: true

Controls the "always-auth" parameter in .npmrc.

    # controlhost_nodejs_npmrc_registries:
      #- registry:
      #  username:
      #  password:

When registries are configured the .npmrc will be provisioned. Please
note that at the moment only one registry can be defined. The username
and password have to be provided in plan. The role will taking care of
creating the auth token.

    #controlhost_vault_pass_path: "{{ controlhost_user_home }}/.ansible/.vault_pass"

When set, the `ANSIBLE_VAULT_PASSWORD_FILE` environment variable in the
.bashrc is set.

    #controlhost_vault_pass_src:

When set the specified file will be copied to the controlhost to
`controlhost_vault_pass_path`.

    controlhost_aws_credentials_path: "{{ controlhost_user_home }}/.aws/credentials"

The path of the aws credentials file. Works together with: `controlhost_aws_credentials_src`.

    #controlhost_aws_credentials_src:

When set the specified file will be copied to the control to
`controlhost_aws_credential_path`.

    #controlhost_default_editor: "nano"

When set the bashrc will export EDITOR with this value.

    controlhost_bashrc: "{{ controlhost_user_home }}/.bashrc"

Path to the bash/shell run commands file for provisioning
`ANSIBLE_VAULT_PASSWORD_FILE` and `EDITOR`.

    controlhost_maven_repositories:
      - id: central
        url: https://repo.maven.apache.org/maven2/
      - id: adobe-public-releases
        url: https://repo.adobe.com/nexus/content/groups/public
        releases:
          enabled: "true"
          updatePolicy: "never"
        snapshots:
          enabled: "false"

List of Maven repositories to be configured in Maven settings.xml. Per
default maven central and the public release repository from Adobe is
configured here.

```
- id: [REPO-ID]
  url: [REPO-URL]
  username: [USERNAME] # optional
  password: [USERNAME] # optional
  releases:  # (optional)
    [KEY]:[VALUE] # will be converted to <key>value</key>
  snapshots: # (optional)
    [KEY]:[VALUE] # will be converted to <key>value</key>
```
List of configuration options for a maven repository.

    controlhost_maven_mirrors: []

List of mirror configurations. See
https://maven.apache.org/guides/mini/guide-mirror-settings.html for
configuration details.

    # Mirror configuration
    #  - id: [REPO-ID]
    #    url: [REPO-URL]
    #    mirrorOf: *

Example for a mirror configuration.

    # controlhost_maven_settings_custom:

When set this part is rendered as is into the <settings></settings>
block of the maven settings.

    #controlhost_ansible_version: 2.5.6

When set the given version of Ansible will be installed on the controlhost.

    controlhost_python_version: 3
    
Controls if Python3 or Python2 will be installed. The exact version depends on the host's OS.

    controlhost_packages_pip_upgrade: true

Controls if pip will be executed using the --upgrade option.

    controlhost_packages_pip_enabled: true

Enables / disables pip package installation.

    controlhost_packages_pip:
      - markupsafe
      - jmespath
      - cffi
      - cryptography
      - boto
      - lxml
      - boto3
      - rsa
      - colorama
      - botocore
      - s3transfer
      - awscli

Pip packages to install. Pip package definition as of When using Ansible
2.7+ you can also specify versions as documented here:
https://docs.ansible.com/ansible/latest/modules/pip_module.html.

    # controlhost_packages_os: []    

List of os packages to install on the controlhost. OS specific, see
variable definition in the vars folder.

    controlhost_packages_os_enabled: true

Enables / disables os package installation.

## Dependencies

This role depends on the following roles:
* [geerlingguy.repo-epel](https://galaxy.ansible.com/geerlingguy/repo-epel) (RedHat OS only)
* [srsp.oracle-java](https://galaxy.ansible.com/srsp/oracle-java)
* [gantsign.maven](https://galaxy.ansible.com/gantsign/maven)
* [andrewrothstein.terraform](https://galaxy.ansible.com/andrewrothstein/terraform)

## Example

Setup the localhost as a controlhost with default editor `NANO`, a
`ANSIBLE_VAULT_PASSWORD_FILE` located in `.ansible/.vault_pass` and
'masterpassword' as Maven master password.

    - hosts: localhost
      vars:
        controlhost_default_editor: nano
        controlhost_vault_pass_path: "{{ controlhost_user_home }}/.ansible/.vault_pass"
        controlhost_maven_master_password: masterpassword
      roles:
        - wcm_io_devops.conga_ansible_controlhost

## License

Apache 2.0
