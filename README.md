# Bitcoin Core Ansible role

Ansible role to install the [Bitcoin Core](https://bitcoincore.org/en/about/) client as a `systemd` service. By default,
it uses sane defaults and some hardening measures for the Systemd service.

By default, all binaries are installed inside `/usr/local/bitcoin-core-<version>/bin` directory. So for example, if you
are installing the version `23.0`, if you want to invoke the `bitcoin-cli` binary, you will need to
use `/usr/local/bitcoin-core-23.0/bin/bitcoin-cli`. Note this is the case for the rest of binaries from Bitcoin.

### Requirements

This role requires a user with `sudo` permissions to work properly.

List of officially supported operating systems:

| ID | Name | Status |
|---------------------|-------------------------------|
| `ubuntu2004`         | Ubuntu 20.04         | :heavy_check_mark:             |
| `ubuntu2204`      | Ubuntu 22.04        |:heavy_check_mark:             |

### Testing

You can execute tests using `molecule`. Install the [`requirements.txt`](molecule) file depending on if you want
to execute tests through Docker or with a VM managed by Vagrant.

```bash
$ molecule test
```

If you want to run a test through a specific operating system you can update the `MOLECULE_DISTRO` variable using
the operating system ID mentioned in the requirements table.

## Variables

You can change some variables to install this role to fit your needs. The default values to install the
Bitcoin node are the following ones:

| Name              	 | Value              	 |
|---------------------|----------------------|
| `bitcoin_user`    	 | `bitcoin`          	 |
| `bitcoin_group`   	 | `bitcoin`          	 |
| `bitcoin_version` 	 | `23.0`             	 |
| `bitcoin_arch`    	 | `x86_64-linux-gnu` 	 |

> If you want to install Bitcoin into a Raspberry you need to change the architecture to `aarch64-linux-gnu`.

To configure the Bitcoin node, you can use the following variables:

| Name                   	     | Value           	 | Note                                             	 |
|------------------------------|-------------------|----------------------------------------------------|
| `bitcoin_data_dir`     	     | `/data/bitcoin` 	 | 	                                                  |
| `bitcoin_network`      	     | `main`          	 | Valid values are: `regtest`, `signet` and `test` 	 |
| `bitcoin_rpc_user`     	     | `yourbtc`       	 | 	                                                  |
| `bitcoin_rpc_password` 	     | `yourbtc`       	 | 	                                                  |
| `bitcoin_zmq_host`     	     | `127.0.0.1`     	 | 	                                                  |
| `bitcoin_bind`     	         | `127.0.0.1`     	 | 	                                                  |
| `bitcoin_rpc_bind`     	     | `127.0.0.1`     	 | This is where to expose the RPC server	            |
| `bitcoin_rpc_allow_ip`     	 | `127.0.0.1`     	 | This can be an IP or a range like `10.0.0.0/24`	   |

### GPG verification

By default, this installer uses `gpg` to verify the integrity and signature of the downloaded artifacts. This
behaviour is controlled by the `bitcoin_pgp_builders_pub_key` field. The content of this structure and default values
are the following ones:

| Name       	 | ID                                         	 |
|--------------|----------------------------------------------|
| `darosior` 	 | `590B7292695AFFA5B672CBB2E13FC145CD3F4304` 	 |
| `guggero`  	 | `F4FC70F07310028424EFC20A8E4256593F177720` 	 |
| `fanquake` 	 | `E777299FC265DD04793070EB944D35F9AC3DB76A` 	 |

If you only want to verify with one user, you should use something like this:

```yaml
bitcoin_pgp_builders_pub_key:
  - id: 590B7292695AFFA5B672CBB2E13FC145CD3F4304
    name: darosior
```

> I use the Guix attestations to verify the release. The data can be found on
> the [Bitcoin Github official repository](https://github.com/bitcoin-core/guix.sigs).
> If the release can't be trusted the role will fail the installation.
