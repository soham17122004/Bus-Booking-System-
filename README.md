

import java.util.*;

class Bus {
    private int busNo;
    private String driverName;
    private String route;
    private int capacity;
    private boolean[] seats;

    public Bus(int busNo, String driverName, String route, int capacity) {
        this.busNo = busNo;
        this.driverName = driverName;
        this.route = route;
        this.capacity = capacity;
        this.seats = new boolean[capacity + 1]; // index 1 to capacity
    }

    public int getBusNo() {
        return busNo;
    }

    public String getDriverName() {
        return driverName;
    }

    public String getRoute() {
        return route;
    }

    public boolean isSeatAvailable(int seatNo) {
        return seatNo > 0 && seatNo <= capacity && !seats[seatNo];
    }

    public void bookSeat(int seatNo) {
        if (seatNo > 0 && seatNo <= capacity)
            seats[seatNo] = true;
    }

    public void cancelSeat(int seatNo) {
        if (seatNo > 0 && seatNo <= capacity)
            seats[seatNo] = false;
    }

    public void showBusInfo() {
        System.out.println("Bus No: " + busNo + " | Driver: " + driverName + " | Route: " + route + " | Capacity: " + capacity);
    }

    public List<Integer> getAvailableSeats() {
        List<Integer> available = new ArrayList<>();
        for (int i = 1; i <= capacity; i++) {
            if (!seats[i]) available.add(i);
        }
        return available;
    }
}

class Booking {
    private static int bookingCounter = 1001;
    private int bookingId;
    private String passengerName;
    private int busNo;
    private int seatNo;
    private String route;

    public Booking(String passengerName, int busNo, int seatNo, String route) {
        this.bookingId = bookingCounter++;
        this.passengerName = passengerName;
        this.busNo = busNo;
        this.seatNo = seatNo;
        this.route = route;
    }

    public int getBookingId() {
        return bookingId;
    }

    public int getBusNo() {
        return busNo;
    }

    public int getSeatNo() {
        return seatNo;
    }

    public void showBooking() {
        System.out.println("Booking ID: " + bookingId + " | Passenger: " + passengerName + " | Bus No: " + busNo +
                " | Seat No: " + seatNo + " | Route: " + route);
    }
}

public class BusBookingSystem {
    private static List<Bus> buses = new ArrayList<>();
    private static List<Booking> bookings = new ArrayList<>();
    private static Scanner scanner = new Scanner(System.in);

    public static Bus findBus(int busNo) {
        for (Bus bus : buses) {
            if (bus.getBusNo() == busNo) {
                return bus;
            }
        }
        return null;
    }

    public static void addBus() {
        System.out.print("Enter Bus No: ");
        int busNo = scanner.nextInt();
        scanner.nextLine();
        System.out.print("Enter Driver Name: ");
        String driver = scanner.nextLine();
        System.out.print("Enter Route: ");
        String route = scanner.nextLine();
        System.out.print("Enter Capacity: ");
        int capacity = scanner.nextInt();

        buses.add(new Bus(busNo, driver, route, capacity));
        System.out.println("✅ Bus added successfully.");
    }

    public static void showBusesByRoute() {
        scanner.nextLine(); // consume newline
        System.out.print("Enter Route: ");
        String route = scanner.nextLine();
        boolean found = false;

        for (Bus bus : buses) {
            if (bus.getRoute().equalsIgnoreCase(route)) {
                bus.showBusInfo();
                found = true;
            }
        }

        if (!found) {
            System.out.println("❌ No buses found on this route.");
        }
    }

    public static void bookSeat() {
        scanner.nextLine(); // consume newline
        System.out.print("Enter Route: ");
        String route = scanner.nextLine();

        List<Bus> routeBuses = new ArrayList<>();
        for (Bus bus : buses) {
            if (bus.getRoute().equalsIgnoreCase(route)) {
                routeBuses.add(bus);
            }
        }

        if (routeBuses.isEmpty()) {
            System.out.println("❌ No buses available on this route.");
            return;
        }

        System.out.println("Available Buses:");
        for (Bus b : routeBuses) {
            b.showBusInfo();
        }

        System.out.print("Enter Bus No to book: ");
        int busNo = scanner.nextInt();
        Bus selectedBus = findBus(busNo);

        if (selectedBus == null) {
            System.out.println("❌ Bus not found.");
            return;
        }

        List<Integer> availableSeats = selectedBus.getAvailableSeats();
        if (availableSeats.isEmpty()) {
            System.out.println("❌ No seats available.");
            return;
        }

        System.out.println("Available Seats: " + availableSeats);
        System.out.print("Choose Seat No: ");
        int seatNo = scanner.nextInt();
        scanner.nextLine(); // consume newline

        if (!selectedBus.isSeatAvailable(seatNo)) {
            System.out.println("❌ Seat is already booked or invalid.");
            return;
        }

        System.out.print("Enter Passenger Name: ");
        String name = scanner.nextLine();

        selectedBus.bookSeat(seatNo);
        bookings.add(new Booking(name, busNo, seatNo, selectedBus.getRoute()));
        System.out.println("✅ Booking successful!");
    }

    public static void cancelBooking() {
        System.out.print("Enter Booking ID to cancel: ");
        int bookingId = scanner.nextInt();
        Booking bookingToRemove = null;

        for (Booking booking : bookings) {
            if (booking.getBookingId() == bookingId) {
                Bus bus = findBus(booking.getBusNo());
                if (bus != null) {
                    bus.cancelSeat(booking.getSeatNo());
                }
                bookingToRemove = booking;
                break;
            }
        }

        if (bookingToRemove != null) {
            bookings.remove(bookingToRemove);
            System.out.println("✅ Booking canceled successfully.");
        } else {
            System.out.println("❌ Booking ID not found.");
        }
    }

    public static void viewAllBookings() {
        if (bookings.isEmpty()) {
            System.out.println("❌ No bookings found.");
            return;
        }

        for (Booking booking : bookings) {
            booking.showBooking();
        }
    }

    public static void viewAllBuses() {
        if (buses.isEmpty()) {
            System.out.println("❌ No buses available.");
            return;
        }

        for (Bus bus : buses) {
            bus.showBusInfo();
        }
    }

    public static void main(String[] args) {
        while (true) {
            System.out.println("\n====== Bus Booking System Menu ======");
            System.out.println("1. Add Bus");
            System.out.println("2. View Buses by Route");
            System.out.println("3. Book Seat");
            System.out.println("4. Cancel Booking");
            System.out.println("5. View All Bookings");
            System.out.println("6. View All Buses");
            System.out.println("7. Exit");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    addBus();
                    break;
                case 2:
                    showBusesByRoute();
                    break;
                case 3:
                    bookSeat();
                    break;
                case 4:
                    cancelBooking();
                    break;
                case 5:
                    viewAllBookings();
                    break;
                case 6:
                    viewAllBuses();
                    break;
                case 7:
                    System.out.println("👋 Thank you for using the Bus Booking System.");
                    return;
                default:
                    System.out.println("❌ Invalid choice.");
            }
        }
    }
}
