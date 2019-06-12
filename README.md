# EMS
# EMS data acqusition in Python from the Meter using ModBus

import csv
from pymodbus.client.sync import ModbusSerialClient as ModbusClient
from pymodbus.constants import Endian
from pymodbus.payload import BinaryPayloadDecoder
from collections import OrderedDict


with open('emsdata.csv','w+', newline ='') as f:
    thewriter = csv.writer(f)
        
    thewriter.writerow(['Voltage','Current','Frequency'])

    thewriter.writerow([''])


#import logging

###----Decalration of Global Variables ----####
# UNIT01 = 0x1
# UNIT02 = 0x2

###----Decalration of Functions ----####
def run_sync_client():
    # client = ModbusClient('localhost', port=5020)
    # from pymodbus.transaction import ModbusRtuFramer
    # client = ModbusClient('localhost', port=5020, framer=ModbusRtuFramer)
    # client = ModbusClient(method='binary', port='/dev/ptyp0', timeout=1)
    # client = ModbusClient(method='ascii', port='/dev/ptyp0', timeout=1)
    f = open('emsdata.csv','w+', newline ='') 
    thewriter = csv.writer(f)
    client = ModbusClient(method='rtu', port='/dev/ttyUSB0', timeout=1,baudrate=9600)
    client.connect()

    rr = client.read_holding_registers(3910, 2, unit=0x01)
    print(rr.registers)

    decoder = BinaryPayloadDecoder.fromRegisters(rr.registers,byteorder=Endian.Big,wordorder=Endian.Little)
    
    thewriter.writerow(['Voltage'])
    value = decoder.decode_32bit_float()
    print(value)
    thewriter.writerow(['%f' %value])
    # print('Voltage is: ') 
    # print( decoder.decode_32bit_float())
    rr = client.read_holding_registers(3912, 2, unit=0x01)
    decoder = BinaryPayloadDecoder.fromRegisters(rr.registers,byteorder=Endian.Big,wordorder=Endian.Little)
    thewriter.writerow(['Current'])
    value = decoder.decode_32bit_float()
    print(value)
    thewriter.writerow(['%f' %value])
    # print('Current is:')
    # print( decoder.decode_32bit_float())
    rr = client.read_holding_registers(3914,2, unit=0x01)
    decoder = BinaryPayloadDecoder.fromRegisters(rr.registers,byteorder=Endian.Big,wordorder=Endian.Little)
    thewriter.writerow(['Frequency'])
    value = decoder.decode_32bit_float()
    print(value)
    thewriter.writerow(['%f' %value])
    # print('Frequency is:')
    # print(decoder.decode_32bit_float())

    # print(rr.registers)   # test the expected value

    # rr = client.read_holding_registers(3911, 2, unit=0x02)
    # print(rr.registers) 

    client.close()
    f.close()

#---Main Program Starts---#

#FORMAT = ('%(asctime)-15s %(threadName)-15s '
#          '%(levelname)-8s %(module)-15s:%(lineno)-8s %(message)s')
#logging.basicConfig(format=FORMAT)
#log = logging.getLogger()
#log.setLevel(logging.DEBUG)





# if __name__ == "__main__":

run_sync_client()
#---Main Program Ends---#
