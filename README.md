# powercontrol

Eine kleine App zur Verwaltung der power_control.sh

#########################################################
#!/system/bin/sh
TOGGLE_FILE="/data/adb/service.d/power_control_enabled"
LOG_FILE="/data/adb/service.d/power_control.log"

# Log: Skript gestartet
echo "$(date) - Skript gestartet" >> $LOG_FILE

while true; do
    if [ -f "$TOGGLE_FILE" ]; then
        echo "$(date) - Datei $TOGGLE_FILE gefunden, Skript wird gestoppt." >> $LOG_FILE
        exit 0
    fi

    CHARGING=$(cat /sys/class/power_supply/battery/status)
    echo "$(date) - Ladezustand: $CHARGING" >> $LOG_FILE

    if [ "$CHARGING" = "Discharging" ]; then
        echo "$(date) - Akku entlaedt sich, 10 Sekunden warten..." >> $LOG_FILE
        sleep 10
        CHARGING_AGAIN=$(cat /sys/class/power_supply/battery/status)
        echo "$(date) - Neuer Ladezustand: $CHARGING_AGAIN" >> $LOG_FILE

        if [ "$CHARGING_AGAIN" = "Discharging" ]; then
            echo "$(date) - Geraet wird ausgeschaltet" >> $LOG_FILE
            reboot -p
        fi
    fi

    sleep 10
done
#########################################################
