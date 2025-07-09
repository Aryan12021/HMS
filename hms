
import mysql.connector

# GLOBAL VARIABLES DECLARATION
myConnection = ""
cursor = ""
userName = ""
password = ""
roomrent = 0
restaurantbill = 0
totalAmount = 0
cid = ""

# Table Creation Statements
createCustomerTable = """CREATE TABLE IF NOT EXISTS c_details (
    cid VARCHAR(20) PRIMARY KEY,
    name VARCHAR(50),
    address VARCHAR(100),
    age VARCHAR(3),
    nationality VARCHAR(50),
    phoneno VARCHAR(15),
    email VARCHAR(50)
)"""

createBookingTable = """CREATE TABLE IF NOT EXISTS booking_record (
    cid VARCHAR(20),
    checkin DATE,
    checkout DATE,
    FOREIGN KEY (cid) REFERENCES c_details(cid)
)"""

createRestaurantTable = """CREATE TABLE IF NOT EXISTS restaurant (
    cid VARCHAR(20),
    choice_dish INT,
    quantity INT,
    bill DECIMAL(10,2),
    FOREIGN KEY (cid) REFERENCES c_details(cid)
)"""

createRoomTable = """CREATE TABLE IF NOT EXISTS room_rent (
    cid VARCHAR(20),
    roomchoice INT,
    roomno VARCHAR(10),
    noofdays INT,
    rent DECIMAL(10,2),
    FOREIGN KEY (cid) REFERENCES c_details(cid)
)"""

# MODULE TO CHECK MYSQL CONNECTIVITY
def MYSQLConnectionCheck():
    global myConnection
    global userName
    global password

    userName = input("\n ENTER MYSQL SERVER'S USERNAME : ")
    password = input("\n ENTER MYSQL SERVER'S PASSWORD : ")
    try:
        myConnection = mysql.connector.connect(
            host="localhost",
            user=userName,
            passwd=password,
            auth_plugin="mysql_native_password"
        )
        
        if myConnection:
            print("\n CONGRATULATIONS ! YOUR MYSQL CONNECTION HAS BEEN ESTABLISHED !")
            cursor = myConnection.cursor()
            cursor.execute("CREATE DATABASE IF NOT EXISTS HMS")
            cursor.execute("USE HMS")
            # Create all required tables
            cursor.execute(createCustomerTable)
            cursor.execute(createBookingTable)
            cursor.execute(createRestaurantTable)
            cursor.execute(createRoomTable)
            cursor.execute("COMMIT")
            cursor.close()
            return True
    except mysql.connector.Error as err:
        print(f"\nERROR ESTABLISHING MYSQL CONNECTION: {err}")
        return False
    finally:
        if 'myConnection' in locals() and myConnection.is_connected():
            myConnection.close() 

def MYSQLConnection():
    global myConnection
    global userName
    global password
    global cursor

    try:
        myConnection = mysql.connector.connect(
            host="localhost",
            user=userName,
            passwd=password,
            database="HMS",
            auth_plugin="mysql_native_password"
        )
        if myConnection:
            return True
        return False
    except mysql.connector.Error as err:
        print(f"\nERROR ESTABLISHING MYSQL CONNECTION: {err}")
        return False

def roomRent():
    global cid
    global roomrent
    customer = searchCustomer()
    if customer:
        if myConnection:
            cursor = myConnection.cursor()
            print("\n### We Have The Following Rooms For You ####")
            print("1. Ultra Royal ----> 10000 Rs.")
            print("2. Royal ---------> 5000 Rs.")
            print("3. Elite ---------> 3500 Rs.")
            print("4. Budget --------> 2500 Rs.")
            roomchoice = int(input("Enter Your Option : "))
            roomno = input("Enter Customer Room No : ")
            noofdays = int(input("Enter No. Of Days : "))
            
            if roomchoice == 1:
                roomrent = noofdays * 10000
                print("\nUltra Royal Room Rent : ", roomrent)
            elif roomchoice == 2:
                roomrent = noofdays * 5000
                print("\nRoyal Room Rent : ", roomrent)
            elif roomchoice == 3:
                roomrent = noofdays * 3500
                print("\nElite Room Rent : ", roomrent)
            elif roomchoice == 4:
                roomrent = noofdays * 2500
                print("\nBudget Room Rent : ", roomrent)
            
            sql = "INSERT INTO room_rent VALUES(%s,%s,%s,%s,%s)"
            values = (cid, roomchoice, roomno, noofdays, roomrent)
            cursor.execute(sql, values)
            cursor.execute("COMMIT")
            print("\nRoom Rent Charged Successfully!")
            cursor.close()
        else:
            print("\nERROR ESTABLISHING MYSQL CONNECTION!")

def totalAmount():
    global cid
    global totalAmount
    customer = searchCustomer()
    if customer:
        if myConnection:
            cursor = myConnection.cursor()
            
            # Get room rent
            cursor.execute("SELECT IFNULL(SUM(rent), 0) FROM room_rent WHERE cid = %s", (cid,))
            room_total = cursor.fetchone()[0]
            
            # Get restaurant bill
            cursor.execute("SELECT IFNULL(SUM(bill), 0) FROM restaurant WHERE cid = %s", (cid,))
            restaurant_total = cursor.fetchone()[0]
            
            totalAmount = float(room_total) + float(restaurant_total)
            
            print("\n### BILL SUMMARY ###")
            print(f"Customer ID: {cid}")
            print(f"Room Rent: Rs. {room_total}")
            print(f"Restaurant Bill: Rs. {restaurant_total}")
            print(f"Total Amount: Rs. {totalAmount}")
            
            cursor.close()
        else:
            print("\nERROR ESTABLISHING MYSQL CONNECTION!") 

def bookingRecord():
    global cid
    customer = searchCustomer()
    if customer:
        if myConnection:
            cursor = myConnection.cursor()
            checkin = input("\nEnter Customer CheckIN Date [ YYYY-MM-DD ] : ")
            checkout = input("\nEnter Customer CheckOUT Date [ YYYY-MM-DD ] : ")
            try:
                sql = "INSERT INTO booking_record VALUES(%s,%s,%s)"
                values = (cid, checkin, checkout)
                cursor.execute(sql, values)
                cursor.execute("COMMIT")
                print("\nCHECK-IN AND CHECK-OUT ENTRY MADE SUCCESSFULLY!")
            except mysql.connector.Error as err:
                print(f"\nError recording booking: {err}")
            finally:
                cursor.close()
        else:
            print("\nERROR ESTABLISHING MYSQL CONNECTION!")

def Restaurant():
    global cid
    global restaurantbill
    customer = searchCustomer()
    if customer:
        if myConnection:
            cursor = myConnection.cursor()
            try:
                print("\n### RESTAURANT MENU ###")
                print("1. Vegetarian Combo --------> 300 Rs.")
                print("2. Non-Vegetarian Combo ----> 500 Rs.")
                print("3. Vegetarian & Non-Vegetarian Combo ----> 750 Rs.")
                
                choice_dish = int(input("Enter Your Choice (1-3): "))
                if choice_dish not in [1, 2, 3]:
                    print("\nInvalid choice! Please select 1, 2, or 3.")
                    return
                
                quantity = int(input("Enter Quantity: "))
                if quantity <= 0:
                    print("\nQuantity must be greater than 0!")
                    return
                
                if choice_dish == 1:
                    print("\nYOU HAVE ORDERED: Vegetarian Combo")
                    restaurantbill = quantity * 300
                elif choice_dish == 2:
                    print("\nYOU HAVE ORDERED: Non-Vegetarian Combo")
                    restaurantbill = quantity * 500
                elif choice_dish == 3:
                    print("\nYOU HAVE ORDERED: Vegetarian & Non-Vegetarian Combo")
                    restaurantbill = quantity * 750
                
                sql = "INSERT INTO restaurant VALUES(%s,%s,%s,%s)"
                values = (cid, choice_dish, quantity, restaurantbill)
                cursor.execute(sql, values)
                cursor.execute("COMMIT")
                print("\nYour Total Bill Amount Is : Rs.", restaurantbill)
                print("\nWE HOPE YOU WILL ENJOY YOUR MEAL!\n")
                
            except ValueError:
                print("\nPlease enter valid numbers for choice and quantity!")
            except mysql.connector.Error as err:
                print(f"\nError recording restaurant order: {err}")
            finally:
                cursor.close()
        else:
            print("\nERROR ESTABLISHING MYSQL CONNECTION!")

# MAIN CODE
def main():
    print("""
    ******************HOTEL MANAGEMENT SYSTEM************************
    ******************CROWN PLAZA************************************

    *********** Designed and Maintained By: *************************
    ******* - AMISHA THAKUR********
    """)

    if MYSQLConnectionCheck():
        while True:
            if not MYSQLConnection():
                print("\nLost connection to database. Please reconnect.")
                break
                
            print("""
    1---->Enter Customer Details
    2---->Booking Record
    3---->Calculate Room Rent
    4---->Calculate Restaurant Bill
    5---->Display Customer Details
    6---->GENERATE TOTAL BILL AMOUNT
    7---->EXIT
    """)
            try:
                choice = int(input("Enter Your Choice: "))
                if choice == 1:
                    userEntry()
                elif choice == 2:
                    bookingRecord()
                elif choice == 3:
                    roomRent()
                elif choice == 4:
                    Restaurant()
                elif choice == 5:
                    searchCustomer()
                elif choice == 6:
                    totalAmount()
                elif choice == 7:
                    if myConnection and myConnection.is_connected():
                        myConnection.close()
                    print("\nThank you for using Hotel Management System!")
                    break
                else:
                    print("\nInvalid Choice! Please try again.")
            except ValueError:
                print("\nPlease enter a valid number!")
            except Exception as e:
                print(f"\nAn error occurred: {e}")
                if myConnection and myConnection.is_connected():
                    myConnection.close()
                break
    else:
        print("\nFailed to establish database connection. Please check your credentials.")

if __name__ == "__main__":
    main() 