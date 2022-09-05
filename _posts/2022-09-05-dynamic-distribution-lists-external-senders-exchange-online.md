## Dynamic Distribution Lists don't like external senders (Exchange Online)

Today I found out that Exchange Online will only deliver internal mail to dynamic distribution lists. External senders will get a NDR with error code 550 5.4.1.

This behaviour seems to be related to a security feature called [Directory-Based Edge Blocking (DBEB)](https://docs.microsoft.com/en-us/exchange/mail-flow-best-practices/use-directory-based-edge-blocking).

Unfortunately, it seems that dynamic distribution lists don't create the required directory entry to allow mail delivery from external senders. In fairness, this is documented at the bottom of the DBED documentation page - but currently not on the *dynamic distribution lists* docs!

The workaround mentioned is to create a mail contact, but it's stated this would only work for hybrid environments. We're planning on moving to full cloud very soon and I didn't want this breaking in future.

Alternatively, it seems you need to change the domain to "Internal Relay" or raise a support case with Microsoft to disable Directory Based Edge Blocking entire. I didn't like the sound of either of these.

My workaround for this is to create a shared mailbox with the alias(es) we want to be usable externally and have that forward to the distribution list. Its not very pretty, but it works.
