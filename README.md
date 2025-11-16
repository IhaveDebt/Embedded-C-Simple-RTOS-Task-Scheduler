#include <stdint.h>
#include <stdio.h>

typedef void (*task_fn)(void);

typedef struct {
    task_fn fn;
    uint32_t interval;
    uint32_t last_run;
} task_t;

uint32_t millis = 0;

void task_led()    { printf("[LED] Blink\n"); }
void task_sensor() { printf("[SENSOR] Reading...\n"); }
void task_log()    { printf("[LOG] System OK\n"); }

task_t tasks[] = {
    { task_led,    200, 0 },
    { task_sensor, 500, 0 },
    { task_log,   1000, 0 }
};

void scheduler_tick(uint32_t ms)
{
    millis += ms;

    for (int i = 0; i < 3; i++)
    {
        if (millis - tasks[i].last_run >= tasks[i].interval)
        {
            tasks[i].fn();
            tasks[i].last_run = millis;
        }
    }
}

int main()
{
    for (int i = 0; i < 20; i++)
        scheduler_tick(100);

    return 0;
}
