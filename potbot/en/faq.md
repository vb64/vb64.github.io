# PotTeleBot FAQ

## Can I add a bot to an existing Telegram group, and not create a new group for the joint expenses?

You can. But each member of this existing group will have to explicitly join the participation in the joint expenses.
In order for a group member to join the joint expenses, he needs to send `/start` command to the group chat.

## We already have a certain amount in the total pot. How can I take it into account in the calculations of the bot?

Group admins in a group chat can use the bot `/cash` command to set the amount in the general box office. To report on refill and withdrawing money from the joint cash, you can use 'Money transfer report' menu in a private chat with a bot. Next you need to select the item 'Group cash'. If you specify a amount with a minus, then this is taking money from the group cash. If without a minus - it is a refill.

## What will happen to the balance of the joint expenses if someone of members leaves the group?

If this participant had a zero balance, then nothing will change. If the leaving participant was to receive some amount from the group cash, this amount will be divided between the remaining members of the group (added to the balance). If the member had a debt to the group cash, then equal shares of this debt will be deducted from the balance sheets of the remaining members of the group.

In any case, bot will report about the changes in the group chat.

## If I remove the bot from the group?

All the data of the joint expenses of this group will be lost. If you later add a bot to this group again, then all calculations will start from scratch.

If you just need to reset the calculations, you not need to delete and re-add the bot. Use the `/reset` command in the group chat. In this case, the list of participants in the joint expenses will be saved, and all balance sheets and cost counters will be reset to zero.

## Will this bot always be free?

Yes.
