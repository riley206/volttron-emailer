# volttron-emailer

`volttron-emailer` allows an instance of the VOLTTRON platform to send
email.  When used in combination with the Alert agent, alerts from
unpublished configured devices will automatically be sent.  In addition,
agents are able to send emails directly through the pub/sub interface.

Agents needing to send an email through the instance can do so by
sending the following header and message to the `platform/send_email` topic
which is monitored by the Emailer agent.  The following
is the expected payload for the message body and the optional header.

## Requires

* python >= 3.10
* volttron >= 10.0

## Installation

Before installing, VOLTTRON should be installed and running.  Its virtual environment should be active.
Information on how to install of the VOLTTRON platform can be found
[here](https://github.com/eclipse-volttron/volttron-core).

Create a directory called `config` and use the change directory command to enter it.

```shell
mkdir config
cd config
```

After entering the config directory, create a file called `emailer_config.json`, use the below JSON to populate your new file. Refer to the configuration overview if needed.

<details>

<summary> Configuration Overview</summary>

## Optional Headers

Emails by default will be sent to the initial configured email addresses. The below headers will overwrite those properties for the current email being sent. In this below example, the headers are "from-address" and "to-addresses". Please fill in the values after the : and within the quotations with your own email addresses

``` json
{
    "from-address": 'foo@bar.com',
    "to-addresses": ['alpha.beta@foo.com', 'bob-and-joe@bar.com']
}
```

## Required Message Body

``` json
{
    "subject": "I am a happy camper",
    "message": "This is a big long string message that I am sending"
}
```

## Example Sending of Email

``` python
headers = {
    "from-address": 'foo@bar.com',
    "to-addresses": ['alpha.beta@foo.com', 'bob-and-joe@bar.com']
}

message = {
    "subject": "I am a happy camper",
    "message": "This is a big long string message that I am sending"
}

self.vip.pubsub.publish('pubsub', topic='platform/send_email',
                        headers=headers, message=message)
```

## Configuration Options

The following JSON configuration file shows all the options currently
supported by the Forward Historian agent.

``` python
{
    # The smtp-address (Simple Mail Transfer Protocol) to ship the email
    # from (the "from-address" to each of the recipients).
    "smtp-address": "smtp.example.com",

    # The smtp-username is to provide the username of the SMTP server
    # which is being used for sending the messages.
    "smtp-username":"<smtp-username>",

    # The smtp-password is to provide the password of the SMTP server
    # corresponding to the username which is being used for sending the messages.
    "smtp-password":"<smtp-password>",

    # The smtp-port is to provide the port of the SMTP server.
    "smtp-port":"<smtp-port>",

    # The smtp-tls yes or no if we want to use TLS.
    "smtp-tls":<true/false>,

    # The sending address of the email.  This value will be listed in the
    # FROM attributed of the message envelop.  It will also be show in the
    # reply of the message when a recipient chooses reply from their
    # email client.
    "from-address": "no-reply@example.com",

    # A list of default email addresses for sending alerts to.  Each
    # address will be sent a copy of the email as if from a mailing list.
    "to-addresses": [
        "admin1@example.com"
    ],

    # When an alert is sent typically it can have the effect of being
    # sent many times.  This setting throttles the sending of email only
    # after a specific number of minutes.
    #
    # DEFAULT: "allow-frequency-minutes": 60
    "allow-frequency-minutes": 120
}
```

</details>

```json
{
    "smtp-address": "<smtp-address>",
    "smtp-username":"<smtp-username>",
    "smtp-password":"<smtp-password>",
    "smtp-port":<smtp-port>,
    "smtp-tls":<true/false>,
    "from-address": "foo@foo.com",
    "to-addresses": [
        "foo1@foo.com",
        "foo2@foo.com"
    ],

    # Only send a certain alert-key message every 120 minutes.
    "allow-frequency-minutes": 120
}
```

Install and start emailer agent.

```shell
vctl install volttron-emailer --tag email --agent-config <path to config> --start --force
```

View the status of the installed agent.

```shell
vctl status
```

To verfiy functionality, Get the [send_email.py](./tests/send_email.py) file and run it with. If all is well, an email will be delivered to the address or addresses specified.

```bash
python3 send_email.py
```

## Development

Please see the following for contributing guidelines [contributing](https://github.com/eclipse-volttron/volttron-core/blob/develop/CONTRIBUTING.md).

Please see the following helpful guide about [developing modular VOLTTRON agents](https://github.com/eclipse-volttron/volttron-core/blob/develop/DEVELOPING_ON_MODULAR.md)

## Disclaimer Notice

This material was prepared as an account of work sponsored by an agency of the
United States Government.  Neither the United States Government nor the United
States Department of Energy, nor Battelle, nor any of their employees, nor any
jurisdiction or organization that has cooperated in the development of these
materials, makes any warranty, express or implied, or assumes any legal
liability or responsibility for the accuracy, completeness, or usefulness or any
information, apparatus, product, software, or process disclosed, or represents
that its use would not infringe privately owned rights.

Reference herein to any specific commercial product, process, or service by
trade name, trademark, manufacturer, or otherwise does not necessarily
constitute or imply its endorsement, recommendation, or favoring by the United
States Government or any agency thereof, or Battelle Memorial Institute. The
views and opinions of authors expressed herein do not necessarily state or
reflect those of the United States Government or any agency thereof.
