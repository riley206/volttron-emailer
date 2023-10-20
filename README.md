Emailer
=======

The Emailer agent allows an instance of the VOLTTRON platform to send
email.  When used in combination with the Alert agent, alerts from
unpublished configured devices will automatically be sent.  In addition,
agents are able to send emails directly through the pub/sub interface.

Agents needing to send an email through the instance can do so by
sending the following header and message to the `platform/send_email` topic
which is monitored by the Emailer agent.  The following 
is the expected payload for the message body and the optional header.

Emailer Installation
----------------
Install volttron-emailer package

```bash
pip install volttron-emailer
```


Start VOLTTRON

```shell
source env/bin/activate
volttron -vv -l volttron.log &>/dev/null &
```

Edit your config file

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
vctl install volttron-emailer --tag email --agent-config volttron-emailer/config --start --force
```

## Testing

To verfiy functionality, Get the [send_email.py](./tests/send_email.py) file and run it with

```bash
python3 send_email.py
```
If all goes well, you should see an email from the address you specified in the config file. 

Optional Headers
----------------

Emails by default will be sent to the initial configured email
addresses. The below headers will overwrite those properties for the
current email being sent.

``` json
{
    "from-address": 'foo@bar.com',
    "to-addresses": ['alpha.beta@foo.com', 'bob-and-joe@bar.com']
}
```


Required Message Body
---------------------

``` json
{
    "subject": "I am a happy camper",
    "message": "This is a big long string message that I am sending"
}
```


Example Sending of Email
------------------------

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


Configuration Options
---------------------

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
