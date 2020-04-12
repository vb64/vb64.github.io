# PotTeleBot FAQ

## Can I add a bot to an existing Telegram group, and not create a new group for the joint expenses?

You can. But each member of this existing group will have to explicitly join the participation in the joint expenses.

The bot will automatically add to the joint expenses the administrators of the group. If the group has option 'all members are admins', then only the creator of the group will be added. In order for a group member to join the joint expenses, he needs to send `/start` command to the group chat.

## We already have a certain amount in the total pot. How can I take it into account in the calculations of the bot?

Group admins in a group chat can use the bot `/cash` command to set the amount in the general box office. To report on refill and withdrawing money from the joint cash, you can use 'Money transfer report' menu in a private chat with a bot. Next you need to select the item 'Group cash'. If you specify a amount with a minus, then this is taking money from the group cash. If without a minus - it is a refill.

## What will happen to the balance of the joint expenses if someone of members leaves the group?

If this participant had a zero balance, then nothing will change. If the leaving participant was to receive some amount from the group cash, this amount will be divided between the remaining members of the group (added to the balance). If the member had a debt to the group cash, then equal shares of this debt will be deducted from the balance sheets of the remaining members of the group.

In any case, bot will report about the changes in the group chat.
