## AuthorizedKeysCommand
From man doc:

Specifies a program to be used to look up the user's public keys. The program must be owned by root,
not writable by group or others and specified by an absolute path. Arguments to AuthorizedKeysCommand
accept the tokens described in the TOKENS section. If no arguments are specified then the username of
the target user is used.
The program should produce on standard output zero or more lines of authorized_keys output.
AuthorizedKeysCommand is tried after the usual AuthorizedKeysFile files and will not be executed if a
matching key is found there. By default, no AuthorizedKeysCommand is run.

tldr; sshd will run command on login attempt and expect that its stdout is a list of public keys and will check if one of them match client public key used to create ssh session.


Container based example:
```
docker build . -t test-ssh-auth-keys-cmd
docker run --rm -it test-ssh-auth-keys-cmd # and run ssh localhost, edit /test.sh to test
```

## How EC2 Instance Connect uses AuthorizedKeysCommand
Amazon Machine Image has sshd configured with:
```
AuthorizedKeysCommand /opt/aws/bin/eic_run_authorized_keys %u %f
AuthorizedKeysCommandUser ec2-instance-connect
```

where `/opt/aws/bin/eic_run_authorized_keys` is a sh script calling `/opt/aws/bin/eic_curl_authorized_keys" "$@"`, next sh script.

Authorized user can inject his key for 60 seconds to instance metadata using:
```
aws ec2-instance-connect send-ssh-public-key \
  --instance-id      <ec2-instance-id> \
  --instance-os-user ec2-user \
  --ssh-public-key   file://~/.ssh/id_rsa.pub
```

We can check if key is present in IMDS with:
```
curl 169.254.169.254/latest/meta-data/managed-ssh-keys/active-keys/ec2-user
```
