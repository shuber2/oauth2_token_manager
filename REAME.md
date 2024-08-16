# oauth2_token_manager

This script allows to obtain oauth2 tokens from Microsoft web services. These
can then be used by software like [mutt](https://www.mutt.org/),
[neomutt](https://neomutt.org/), [msmtp](http://msmtp.sourceforge.net/),
[offlineimap](http://www.offlineimap.org/),
[mbsync](http://isync.sourceforge.net/) to access our email accounts at
[Micorsoft 365](https://en.wikipedia.org/wiki/Microsoft_365)[^office365] or
Gmail.

Large parts of this script are based on
[mutt_oauth2.py](https://github.com/muttmua/mutt/blob/master/contrib/mutt_oauth2.py)
by Alexander Perlis found in the mutt repository. It compares as follows:

  - The token file encryption is symmetric and performed by openssl rather than
    gpg. This makes it easier to integrate with password or wallet managers
    without reoccurring interactions with gpg-agent.

  - The token files are stored in `~/.cache/` or whatever the equivalent on
    your platform is.

  - The entire code base has been largely redesigned, various authorization
    variations have been stripped and the test code for IMAP, POP and SMTP
    endpoints has been removed.

  - The original script also supported Google services, while this one at the
    moment only supports Microsoft services.

## Installation

This script is written in Python 3 and requires the following packages:

  - `requests`
  - `platformdirs`

These can be installed using pip:

```sh
pip install -r requirements.txt
```


## Usage

On first use, authenticate for some account and store the token in a
token file `myaccount`:

```sh
oauth2_token_manager -p password -t myaccount -a
```

On subsequent uses, retrieve the token as follows:
```sh
oauth2_token_manager -p password -t myaccount
```

Instead of passing "password" on the command line, it is **highly recommended**
to pass it by a password manager!

```sh
# For example, with pass:
oauth2_token_manager -p $(pass show oauth2_token_manager/myaccount)

# Or with kwallet:
oauth2_token_manager -p $(kwallet-query kdewallet -f oauth2_token_manager -r myaccount)
```

In case of kallet, you have to create the folder "oauth2_token_manager" and
create the entry "myaccount" in the kwallet manager. You can check the
available entries with:

```sh
kwallet-query kdewallet -f oauth2_token_manager -l
```


## Example with mbsync

I use this script together with mbsync to sync my mail. In `~/.mbsyncrc` you can use the following config:

```
IMAPAccount myaccount
Host outlook.office365.com
User username@example.com
AuthMechs XOAUTH2
PassCmd "oauth2_token_manager -p $(kwallet-query kdewallet -f oauth2_token_manager -r myaccount) -t myaccount"
SSLType IMAPS
```


[^office365]: I gave up tracking the different product, marketing and
    technology names Microsoft uses for their email services. I think it is
    called Microsoft 365 now. But I am not sure. I am sure that it will change
    again in the future. (Paragraph phrased by GitHub's Copilot.)
