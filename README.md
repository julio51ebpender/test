# CS2-Steam-Logon-Disconnect-Exploit
Server.dll exploit allowing user control of server disconnect value, leading to arbitrary disconnect reasons from the client, can be used to force cancel matches by causing a fake vac ban.

The match canceller mechanism was discovered by others previously, however at the time they were just setting disconnect reason manually.

I discovered the No Steam Logon mechanism that allows you to do this to this day.

Others are selling this, who never put in any effort to figure it out themselves.

Enjoy while it lasts.

This is a shitty old copy of the PoC code that is NOT UPDATED, If you're smart you'll figure it out.

Press F8 when respawning

```cpp
void CCSGameRules_ClientCommandKeyValues(CCSGameRules *rules,
                                         CBasePlayerController *controller,
                                         KeyValues *kv)
{
    if (!controller)
        return;

    const char *root = kv->GetName();

    if (strcmp_like(root, "InvalidSteamLogon") == 0) {
        CBasePlayerPawn *pawn = ResolveHandle(controller->m_hPawn);

        if (!pawn)
            return;

        if (rules->byte_D9 || rules->byte_D8 || !pawn->vfunc_0x540()) {
            kv->SetInt("disconnect", 1);
        } else {
            int reason = kv->GetInt("reason", 0);
            if (reason == 0)
                reason = 9;

            // Inferred name from downstream consumers; SDK does not name this field.
            pawn->pending_invalid_steam_logon_disconnect_reason = reason; // pawn + 0xCD8
        }

        return;
    }

    if (strcmp_like(root, "CrosshairCode") == 0) {
        KeyValues *code_key = kv->FindKey("code");
        const char *code = "";

        if (code_key)
            code = code_key->GetString("", 0, 0);

        if (code && code[0] && strlen(code) < 35)
            SetControllerCrosshairCodes(controller);

        return;
    }
}
```
