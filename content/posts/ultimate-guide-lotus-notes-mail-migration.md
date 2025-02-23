---
date: 2010-09-09 12:40:11
title: Ultimate guide of Lotus Notes mail migration
category: English
tags: Dovecot, email, Lotus Notes, IMAP, Mac OS X 10.5 Leopard, Linux, macOS, maildir, Microsoft, Windows, Apple, TCPIP, port, Python, Mac OS X 10.6 Snow Leopard
---

The title may sounds pretentious but extracting mails out of Lotus Notes is
soooo hard and complicated, that achieving such a task feels like winning an
epic battle against the forces of evil.

Anyway. The goal of this post is to help you migrate all your Lotus Notes mails
to a more convenient and standard format like maildir or mailbox.

There are several ways of extracting all your mail trapped in Lotus Notes'
proprietary databases.


## Method #1: using integrated IMAP service

This is probably the simplest method. It consists in using the Lotus Notes
desktop client (were your emails currently resides) as an IMAP client.

Essentially, what you have to do is just to create a secondary account linked
to an IMAP server, like Gmail, etc. This works well and is [explained in
details in this tutorial
](https://salesittech.blogspot.com/2009/02/transfer-lotus-notes-email-to-gmail-and.html).

But sometimes your Notes client is behind firewalls and proxys. So you can't
reach the Internet.

And some other times, Lotus Notes clients are crippled and don't let you create
an IMAP connexion. Unfortunately this happened to me:

![](/uploads/2010/missing-lotus-notes-imap-template.png)

So I had to found another approach.


## Method #2: using `nlconverter`

This method is [explored in details in my previous article
](https://kevin.deldycke.com/2010/09/convert-lotus-notes-nsf-files-mbox-nlconverter/).

But again, it seems that the Lotus Notes on my machine was crippled and/or
corrupted. The `nlconverter` GUI gave me this error:

![](/uploads/2010/nlconverter-gui-fail.png)

And the command line gave me this:

    ```pytb
    Traceback (most recent call last):
      File "notes2mbox.py", line 21, in <module>
        db = NlconverterLib.getNotesDb(notesNsfPath, notesPasswd)
      File "C:\winnlc-alpha-1\NlconverterLib.py", line 43, in getNotesDb
        session = win32com.client.Dispatch(r'Lotus.NotesSession')
      File "C:\Python26\lib\site-packages\win32com\client\__init__.py", line 95, in Dispatch
        dispatch, userName = dynamic._GetGoodDispatchAndUserName(dispatch,userName,clsctx)
      File "C:\Python26\lib\site-packages\win32com\client\dynamic.py", line 104, in _GetGoodDispatchAndUserName
        return (_GetGoodDispatch(IDispatch, clsctx), userName)
      File "C:\Python26\lib\site-packages\win32com\client\dynamic.py", line 84, in _GetGoodDispatch
        IDispatch = pythoncom.CoCreateInstance(IDispatch, None, clsctx, pythoncom.IID_IDispatch)
    pywintypes.com_error: (-2147221231, 'ClassFactory ne peut pas fournir la classe demand\xe9e', None, None)
    ```

After these two failed attempts, I was quite depressed and not far from
surrender to the evil power of proprietary software. Then I managed to setup a
new (but complicated) strategy.


## Method #3: using Lotus Notes client for Mac OS X

This is the only method that worked for me, and basically, is the same as the
first one, but on Mac OS X. Additionally, it involves a local mail server. This
procedure was tested several times on [Mac OS X Leopard
](https://amzn.com/B000FK88JK/?tag=kevideld-20).

  1. First things first, [download the trial version of Lotus Notes
  ](https://www.ibm.com/developerworks/downloads/ls/lsndad/?S_TACT=105AGX28&S_CMP=TRIALS)
  client for Mac OS X (unfortunately you have to register) and install it. I
  used Lotus Notes 7.0.3 for Mac OS X Leopard:

![](/uploads/2010/lotus-notes-mac-osx-leopard-install.png)

  2. You'll be welcomed by a [wizard
  ](https://en.wikipedia.org/wiki/Wizard_(software)):

![](/uploads/2010/lotus-notes-wizard-screen-1.png)

  3. On the next screen, enter a dummy name and uncheck the
  "_I want to connect to a Domino server_" box:

![](/uploads/2010/lotus-notes-wizard-screen-2.png)

  4. Then proceed to the next step in which you'll uncheck
  "_Setup instant messaging_":

![](/uploads/2010/lotus-notes-wizard-screen-3.png)

  5. In the last screen, uncheck all boxes. We don't want to setup any other
  service yet:

![](/uploads/2010/lotus-notes-wizard-screen-4.png)

  6. Initial setup is now complete:

![](/uploads/2010/lotus-notes-wizard-screen-5.png)

You can now launch Lotus Notes:

![](/uploads/2010/launch-lotus-notes-for-mac-osx.png)

![](/uploads/2010/lotus-notes-7-on-mac-osx-leopard.png)

  7. On first run, there will be the following screen, where you should click
  on the "_No thanks, just give me the defaults_":

![](/uploads/2010/lotus-notes-wizard-screen-6.png)

  8. You'll end up on what will be your default Lotus Notes main page from now
  on:

![](/uploads/2010/lotus-notes-wizard-screen-7.png)

  9. The next step is to go back to the machine (Windows for me) from which
  you're running the Notes client containing all the mails you plan to migrate.
  From there, [export your mail database
  ](https://kevin.deldycke.com/2010/06/how-to-export-backup-lotus-notes-mails/):

![](/uploads/2010/export-screen.png)

All details of this operation can be found in [this dedicated article
](https://kevin.deldycke.com/2010/06/how-to-export-backup-lotus-notes-mails/).

  10. Then go back to your Mac OS X machine and import your freshly exported
  `.nsf` database. This is as simple as opening the file via the `File` >
  `Database` > `Open...` menu and dialog:

![](/uploads/2010/lotus-notes-database-opening.png)

![](/uploads/2010/lotus-notes-open-database-dialog.png)

  11. When Notes attempts to open the file, you'll be prompted by several
  dialogs regarding the security attached to the database. If you get the
  "_Create Cross Certificate_" screen, then just answer "_Yes_" as below:

![](/uploads/2010/lotus-notes-cross-certificate-creation-dialog.png)

And every time you get an "_Execution Security Alert_" message, always check
the "_Start trusting the signer to execute this action_" option before clicking
"_OK_":

![](/uploads/2010/lotus-notes-execution-security-alert-dialog.png)

  12. The client will then rebuild the index before giving you a plain view of
  your inbox:

![](/uploads/2010/lotus-notes-database-import.png)

![](/uploads/2010/lotus-notes-database-successful-import.png)

  13. Next step is to setup a local IMAP server:

![](/uploads/2010/dovecot-on-macosx.png)

As you can see I [used Dovecot, and all is explained here
](https://kevin.deldycke.com/2010/04/setup-lightweight-imap-server-macos-leopard-dovecot/).

  14. Now it's time to create a new account in Lotus Notes to access this local
  IMAP server. Click on the `Address Book` in your toolbar and add a new
  `Account`:

![](/uploads/2010/lotus-notes-address-book-icon.png)

![](/uploads/2010/lotus-notes-create-new-mail-account.png)

  15. Here is where you configure Notes to let it be aware of our local server
  existence. Only the first tab must be changed to your local parameters. You
  can left the last two tabs untouched:

![](/uploads/2010/lotus-notes-local-imap-config.png)

  16. Open within Notes your local IMAP mailbox. It is found in the workspace,
  which you can access via the `Databases` icon on your toolbar:

![](/uploads/2010/lotus-notes-open-workspace.png)

![](/uploads/2010/lotus-notes-workspace.png)

  17. You'll be welcomed by a useless help screen:

![](/uploads/2010/lotus-notes-imap-welcome-screen.png)

Just close it to get your local IMAP mail view:

![](/uploads/2010/lotus-notes-local-imap-mailbox-view.png)

  18. While trying to opening the local IMAP mailbox, you may encounter this
  `TCPIP port` error:

![](/uploads/2010/lotus-notes-tcp-ip-error.png)

In this case, please have a look at my other [article explaining how to open
TCPIP port in Lotus Notes
](https://kevin.deldycke.com/2010/08/how-to-fix-lotus-notes-disabled-tcp-ip-port-error/).

  19. For this step, just copy or cut, then paste, mails from your local `.nsf`
  database to your local IMAP account:

![](/uploads/2010/lotus-notes-copy-nsf-mails.png)

![](/uploads/2010/lotus-notes-copying-nsf-mails.png)

![](/uploads/2010/lotus-notes-paste-mails-to-local-imap.png)

![](/uploads/2010/lotus-notes-pasting-mails-to-local-imap.png)

![](/uploads/2010/lotus-notes-mails-migrated-to-local-imap.png)

  20. While playing with copy'n'paste, you may encounter this error:

![](/uploads/2010/notes-rich-text-to-mime-conversion-error.png)

A [workaround can be found in this article
](https://kevin.deldycke.com/2010/05/lotus-notes-rich-text-mime-conversion-error/).

  21. Finally, if like me you've played a lot with mails during the transfer
  step above, you may ends up with loads of duplicate mails. In this case have
  a look at the [deduplication script
  ](https://kevin.deldycke.com/2010/08/maildir-deduplication-script-python/) I
  wrote. It will help you clean-up your Maildir folder.

  22. That's it! You now have a standard Maildir of your Lotus Notes mails,
  located in your user home directory (`~/Maildir`):

![](/uploads/2010/maildir-containing-lotus-notes-mails.png)

My ultimate action was to [convert the Dovecot maildir to Kmail maildir
](https://kevin.deldycke.com/2007/11/how-to-import-a-maildir-folder-to-kmail/),
as I wanted to use Kmail to finally upload everything in Gmail. But you can use
anything that suit your needs, like [thunderbird
](https://www.mozillamessaging.com/thunderbird/) or any mail conversion tools.


## Method #4: Lotus Notes client v8.5 on Mac OS X Leopard

The same procedure as in method #3 can be performed with the trial version of
the Lotus Notes client v8.5, running on [Mac OS X Snow Leopard
](https://amzn.com/B001AMHWP8/?tag=kevideld-20).

Here are the equivalent screenshots:

![](/uploads/2010/010-lotus-notes-mac-install.png)

![](/uploads/2010/015-lotus-notes-mac-install-components.png)

![](/uploads/2010/020-lotus-notes-mac-install-finished.png)

![](/uploads/2010/030-lotus-notes-wizard-welcome.png)

![](/uploads/2010/040-lotus-notes-wizard-config-1.png)

![](/uploads/2010/050-lotus-notes-wizard-config-2.png)

![](/uploads/2010/060-lotus-notes-wizard-finished.png)

![](/uploads/2010/065-lotus-notes-mac-splash-screen.png)

![](/uploads/2010/070-lotus-notes-start-init.png)

![](/uploads/2010/080-lotus-notes-default-email-program.png)

![](/uploads/2010/090-lotus-notes-8-5-mac-default-screen.png)

![](/uploads/2010/100-lotus-notes-open-nsf-database.png)

![](/uploads/2010/110-lotus-notes-open-database-dialog.png)

![](/uploads/2010/120-lotus-notes-certificate-dialog.png)

![](/uploads/2010/130-lotus-notes-security-alert-dialog.png)

![](/uploads/2010/140-lotus-notes-opened-nsf-database.png)

![](/uploads/2010/150-lotus-notes-open-preferences.png)

![](/uploads/2010/160-lotus-notes-create-new-account.png)

![](/uploads/2010/170-lotus-notes-new-account-parameters.png)

![](/uploads/2010/180-lotus-notes-open-local-imap.png)

![](/uploads/2010/190-lotus-notes-local-imap-inbox-view.png)

![](/uploads/2010/200-lotus-notes-copy-nsf-mails.png)

![](/uploads/2010/210-lotus-notes-paste-mails-to-local-imap.png)

![](/uploads/2010/220-lotus-notes-mails-migrated-to-local-imap.png)


## Method #5: Lotus Notes client on Windows XP

I just tried the first method again (IMAP connection with Note's fat client).
But this time I installed Lotus Notes 8.5.2 trial version on a fresh [Windows
XP](https://amzn.com/B0002423YK/?tag=kevideld-20) running within a QEMU
instance.

As you can see, it works:

![](/uploads/2010/lotus-notes-imap-mail-migration-via-qemu.png)

With simple copy'n'paste, I was able to migrate several batch of mails. Until
the target `notes-import` folder on my Gmail account reached 2000+ mails, at
which point my Notes client freezed hard. So I'm stuck with only part of my
mails migrated. Again, this is not the solution I'm looking for, as it can't
handle large quantity of mails. :(


## Conclusion

  * Lotus Notes sucks. [Everybody knows that
  ](https://www.guardian.co.uk/technology/2006/feb/09/guardianweeklytechnologysection),
  but I feel liberated saying that! ;)

  * The smartest thing to do is to avoid Notes like the plague in the first
  place. Sadly when working for the man, it's not always possible... :(

  * The only method I found to work for me (the third solution in this article)
  is far from perfect from my point of view. What I dream about is a 100%
  automated solution, like a command line utility we can name `nsf2maildir`.
  And as I don't plan to own Apple hardware and software in a near future, such
  a command should be 100% free software and running on Linux. I really think
  there is a "market" for a free software component able to read and understand
  `.nsf` files. Any motivated volunteer? ;)
