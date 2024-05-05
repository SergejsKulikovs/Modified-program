# Modified-program
#include "mbed.h"

// EventQueue and Thread are used to safely handle the button press event outside of the ISR.
// This prevents any unsafe operations within the ISR and offloads the task of handling the button press to a separate thread that executes in a normal thread context.
EventQueue queue;
Thread eventThread;

InterruptIn button(USER_BUTTON);

void printMessage() {
    printf("Button pressed\n");
}

void onButtonPress() {
    queue.call(&printMessage);
}

int main() {
    // Start the event thread
    eventThread.start(callback(&queue, &EventQueue::dispatch_forever));

    // Attach the ISR to handle button press events
    button.rise(&onButtonPress);

    while (true) {
        ThisThread::sleep_for(1000ms);
    }
}
