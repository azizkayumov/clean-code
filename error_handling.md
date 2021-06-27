Error handling is important, but **if it obscures logic, it's wrong.**

### Use Exceptions Rather Than Return Codes
```
public void sendShutDown(){
    DeviceHandle handle = getHandle(DEV1);
    // check the state of the device
    if (handle != DeviceHandle.INVALID){
        // save the device status
        retrieveDeviceRecord(handle);
        // if not suspended, shut down
        if (record.getStatus() != DEVICE_SUSPENDED){
            pauseDevice(handle);
            clearDeviceWorkQueue(handle);
            closeDevice(handle);
        }else{
            logger.log("Device suspended. Unable to shut down");
        }
    }else{
        logger.log("Invalid handle for:" + DEV1.toString());
    }
}
```
The problem with error codes is the caller must check for errors immediately after the call. Unfortunately, it's easy to forget. So, it is better to throw an exception when you encounter an error. The calling code is cleaner and its logic is not obscured by error handling:
```
public void sendShutDown(){
    try{
      tryToShutDown();
    }catch(DeviceShutDownError e){
      logger.log(e);
    }
}

private void tryToShutDown() throws DeviceShutDownError{
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    
    pauseDevice(handle);
    clearDeviceWorkQueue(handle);
    closeDevice(handle);
}

private DeviceHandle getHandle(DeviceID id){
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
}

...
```
Notice how cleaner it is. This is not about a matter of aesthetics, the code is better because two concerns were tangled: the logic for device shutdown and error handling, are now separated!

### Write Your *Try-Catch-Finally* Statement First
First, try to write tests that force exceptions, and then add behavior to your handler to satisfy your tests. This will cause you to build the transaction scope of the **try** block first and will help you maintain the transaction nature of the scope.
```
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName(){
    sectionStore.retrieveSection("invalid - file");
}
```
Create a dummy stub:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    return new ArrayList<RecordedGrip>();
}
```
Next, force to throw the expected exception:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    try{
        FileInputStream stream = new FileInputStream(sectionName);
    }catch(Exception e){
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```
Finally, add the real implementation and narrow down the scope of caught exceptions:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    try{
        FileInputStream stream = new FileInputStream(sectionName);
        stream.close();
    }catch(FileNotFoundException e){
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```
