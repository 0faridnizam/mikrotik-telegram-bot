# Device Login Notification

The script will send a notification about the successful login and end of the user’s session on the MikroTik device. The notification is sent to Telegram.

### Example messages:
> user matcha logged in from 6C:3B:XX:XX:XX:XX via winbox – user is logged in to the device using WinBox;

> user matcha logged in from 10.12.12.22 via winbox - user is logged in to the device using WinBox;

### Create script:
```mikrotik
:local DeviceName [/system identity get name];
:local Time [/system clock get time];
:local Date [/system clock get date];
:local EmailMessageText;
:local TelegramMessageText;

:global ParseLogAccountEndArrayID;

:local IDsEventsAccount [/log find where  topics ~ "account"];

:local LenArrayIDs [:len $IDsEventsAccount];
:local StartArrayID [:find $IDsEventsAccount $ParseLogAccountEndArrayID];
:local EndArrayID ($IDsEventsAccount -> ($LenArrayIDs-1));

:if ($EndArrayID != $ParseLogAccountEndArrayID and [:tobool $ParseLogAccountEndArrayID] ) do={

    :for KeyArray from=($StartArrayID+1) to=($LenArrayIDs-1) do={
        :local IDMessage ($IDsEventsAccount ->$KeyArray );
        :set EmailMessageText "$EmailMessageText \n\r  $[/log get number=$IDMessage time] - $[/log get number=$IDMessage message];";
        :set TelegramMessageText "$TelegramMessageText %0D%0A  $[/log get number=$IDMessage time] - $[/log get number=$IDMessage message];";
        }

    # START SEND TELEGRAM
    :local MessageText "\F0\9F\94\93 <b>$DeviceName: AUTH</b> $TelegramMessageText";
    :local SendTelegramMessage [:parse [/system script  get MyTGBotSendMessage source]];
    $SendTelegramMessage MessageText=$MessageText;
    # END SEND TELEGRAM
}

:set ParseLogAccountEndArrayID $EndArrayID;
```
