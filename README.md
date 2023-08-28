
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

int validateTimeFormat(int hours, int minutes, int seconds) {
    if (hours < 0 || hours > 23 ||
        minutes < 0 || minutes > 59 ||
        seconds < 0 || seconds > 59) {
        return 0;
    }
    else
    return 1;
}

int main() {
    time_t current_local_time;
    time(&current_local_time);

    struct tm *local_timeinfo = localtime(&current_local_time);

    int ist_offset_seconds = 5 * 60 * 60 + 30 * 60;
    time_t ist_current_time = current_local_time + ist_offset_seconds;

    struct tm *ist_current_timeinfo = localtime(&ist_current_time);

    char ist_current_time_str[9];
    strftime(ist_current_time_str, sizeof(ist_current_time_str), "%H:%M:%S", ist_current_timeinfo);
    printf("Current IST Time: %s\n", ist_current_time_str);

    int alarm_hour, alarm_minute, alarm_second;
    printf("Enter the alarm time (IST, HH:MM:SS): ");
    scanf("%d:%d:%d", &alarm_hour, &alarm_minute, &alarm_second);

    if (alarm_hour == 0 && alarm_minute == 0 && alarm_second == 0) {
        printf("Error: Invalid alarm time.\n");
        return 1;
    }

    if (!validateTimeFormat(alarm_hour, alarm_minute, alarm_second)) {
        printf("Error: Invalid time format or out-of-bound values.\n");
        return 1;
    }

    ist_current_timeinfo->tm_hour = alarm_hour;
    ist_current_timeinfo->tm_min = alarm_minute;
    ist_current_timeinfo->tm_sec = alarm_second;

    if (alarm_second >= 60) {
        alarm_second = alarm_second % 60;
        alarm_minute += 1;
    }
    if (alarm_minute >= 60) {
        alarm_minute = alarm_minute % 60;
        alarm_hour += 1;
    }
    if (alarm_hour >= 24) {
        alarm_hour = alarm_hour % 24;
    }

    time_t alarm_time = mktime(ist_current_timeinfo);

    char alarm_time_str[9];
    strftime(alarm_time_str, sizeof(alarm_time_str), "%H:%M:%S", ist_current_timeinfo);
    printf("Alarm set for IST Time: %s\n", alarm_time_str);

    double time_remaining = difftime(alarm_time, ist_current_time);
    if (time_remaining <= 0) {
        printf("Error: Alarm time is in the past.\n");
        return 1;
    }
    printf("Time remaining for alarm: %.0f seconds\n", time_remaining);

    sleep((unsigned int)time_remaining);

    printf("ALARM!!!!!!!!\n");

    return 0;
}
