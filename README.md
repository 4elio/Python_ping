# Python_ping
# How to make a ping whit a python script


import os
import socket
import datetime
import time

'''Innanzitutto, verrà eseguito first_check() (questa funzione verrà eseguita solo una volta cioè all'inizio dello script). 
   Se true(connessione acquisita): verrà avviato il monitoraggio Se false(PING NON ARRIVA ALLA DESTINAZIONE): 
   else verrà eseguita l'istruzione avendo infinito ciclo while per verificare la connessione internet utilizzando la funzione ping() Se ping restituisce false, 
   il ciclo verrà eseguito dopo ogni secondo fino a quando la funzione ping() non restituisce true Se il ping restituisce true(connessione acquisita), il ciclo si interromperà e verrà avviato il monitoraggio .
   
   In secondo luogo, per monitorare la connessione di rete, verrà eseguita l'istruzione first while, che è un ciclo infinito Se il ping restituisce true, 
   il ciclo verrà eseguito ogni 5 secondi finché non restituisce false Se il ping restituisce false, verrà stampato il tempo di inattività e il ciclo verrà eseguito dopo ogni secondo 
   fino a quando non viene ripristinata una connessione Internet. 
   Dopo il ripristino della connessione Internet, verranno stampati i tempi di attività e di indisponibilità e l'iterazione tornerà all'inizio del ciclo'''




FILE = os.path.join(os.getcwd(), "ping_status.log")


# Il sistema tenterà di eseguire il ping di un server specifico (PORT su un IP) Se la macchina non riesce a connettersi,
# verrà eseguita l'istruzione EXCEPT In caso contrario, la connessione verrà chiusa dopo che il sistema
# è stato connesso correttamente al server
def ping():

    try:
        socket.setdefaulttimeout(3)

        # if data interruption occurs for 3
        # seconds, <except> part will be executed

        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        # AF_INET: address family
        # SOCK_STREAM: type for TCP

        host = input ("\n""Inserisci l'indirizzo da verificare")
        port = int(input("\n""Inserisci la porta" ))

        server_address = (host, port)
        s.connect(server_address)

    except OSError as error:
        return False
    # function returns false value
    # after data interruption

    else:
        s.close()
        # closing the connection after the
        # communication with the server is completed
        return True

'''Il tempo di indisponibilità è la durata per la quale la connessione Internet non era disponibile.
 Viene calcolato utilizzando il tempo di inattività (stop) quando la connessione Internet
 è stata persa e il tempo di attività (inizio) quando la connessione Internet è stata ripristinata'''

def calculate_time(start, stop):
''' #calculating unavailability
    # time and converting it in seconds'''
    difference = stop - start
    seconds = float(str(difference.total_seconds()))
    return str(datetime.timedelta(seconds=seconds)).split(".")[0]

''' Questa funzione verrà eseguita una sola volta, cioè all'inizio dello script per verificare se il sistema è già connesso a una connessione Internet o meno, e scriverlo nel file di registro.
 La funzione ping() viene chiamata Se ping restituisce true (la macchina è connessa a Internet),
 lo script stamperà "CONNECTION ACQUIRED" e scriverà lo stesso nel file di registro.
 Se il ping restituisce false (il sistema non è connesso a Internet), lo script stamperà "CONNESSIONE NON ACQUISITA" e scriverà lo stesso nel file di registro.'''

def first_check():
    if ping():
        # if ping returns true
        live = "\nPING ARRIVATO A DESTINAZIONE\n"
        print(live)
        connection_acquired_time = datetime.datetime.now()
        acquiring_message = "connessione acquisita " + \
                            str(connection_acquired_time).split(".")[0]
        print(acquiring_message)

        with open(FILE, "a") as file:

            # writes into the log file
            file.write(live)
            file.write(acquiring_message)

        return True

    else:
        # if ping returns false
        not_live = "\nPING NON ARRIVA ALLA DESTINAZIONE\n"
        print(not_live)

        with open(FILE, "a") as file:

            # writes into the log file
            file.write(not_live)
        return False


def main():
   ''' # main function to call functions'''
    monitor_start_time = datetime.datetime.now()
    monitoring_date_time = "monitoring started at: " + \
                           str(monitor_start_time).split(".")[0]

    if first_check():
        # if true
        print(monitoring_date_time)
''' # monitoring will only start when
    # the connection will be acquired
'''
    else:
        '''# if false'''
        while True:

            '''# infinite loop to see if the connection is acquired'''
            if not ping():

                '''# if connection not acquired'''
                time.sleep(1)
            else:
'''
                # if connection is acquired
'''
                first_check()
                print(monitoring_date_time)
                break

    with open(FILE, "a") as file:
'''
        # write into the file as a into networkinfo.log,
        # "a" - append: opens file for appending,
        # creates the file if it does not exist???
   '''
        file.write("\n")
        file.write(monitoring_date_time + "\n")

    while True:
'''
        # infinite loop, as we are monitoring
        # the network connection till the machine runs
   '''   
        if ping():
'''
            # if true: the loop will execute after every 5 seconds
   '''
            time.sleep(5)

        else:
       '''     # if false: fail message will be displayed
          '''
            down_time = datetime.datetime.now()
            fail_msg = "disconnected at: " + str(down_time).split(".")[0]
            print(fail_msg)

            with open(FILE, "a") as file:
                # writes into the log file
                file.write(fail_msg + "\n")

            while not ping():
             '''
                # infinite loop, will run till ping() return true
                '''
                time.sleep(1)

            up_time = datetime.datetime.now()
'''
            # after loop breaks, connection restored
            uptime_message = "connected again: " + str(up_time).split(".")[0]
'''
            down_time = calculate_time(down_time, up_time)
            unavailablity_time = "connection was unavailable for: " + down_time

            print(uptime_message)
            print(unavailablity_time)

            with open(FILE, "a") as file:
'''
                # log entry for connection restoration time,
                # and unavailability time
   '''
                file.write(uptime_message + "\n")
                file.write(unavailablity_time + "\n")


main()

