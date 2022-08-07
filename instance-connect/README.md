#
```
docker build . -t test-ssh-auth-keys-cmd
docker run --rm -it test-ssh-auth-keys-cmd # and run ssh localhost, edit /test.sh to test
```

# ec2
```
AuthorizedKeysCommand /opt/aws/bin/eic_run_authorized_keys %u %f
AuthorizedKeysCommandUser ec2-instance-connect
```

where `/opt/aws/bin/eic_run_authorized_keys` is a sh script calling `/opt/aws/bin/eic_curl_authorized_keys" "$@"`, next sh script.

```
curl 169.254.169.254/latest/meta-data/managed-ssh-keys/active-keys/ec2-user
aws ec2-instance-connect send-ssh-public-key \
  --instance-id      <ec2-instance-id> \
  --instance-os-user ec2-user \
  --ssh-public-key   file://~/.ssh/id_rsa.pub
```
