import pytest
import subprocess
import platform
import psutil
import os
import logging
import requests

# Set up logging
logging.basicConfig(filename='test_results.log', level=logging.INFO)
@pytest.fixture(scope="module")
def disk_drives():
    if platform.system() == "Windows":
        # Use the wmic command to retrieve disk drive information on Windows
        result = subprocess.run(["wmic", "diskdrive", "list", "brief"], capture_output=True, text=True)
        disk_drives = [line.split()[1] for line in result.stdout.strip().split("\n") if line.strip()]
    else:
        # Use lsblk on non-Windows systems (assumed to be Unix-like)
        result = subprocess.run(["lsblk", "-o", "NAME", "-nl"], capture_output=True, text=True)
        disk_drives = result.stdout.strip().split("\n")
    return disk_drives

def get_disk_drives():
    disk_drives = []

    # Get all disk partitions
    partitions = psutil.disk_partitions(all=True)

    # Iterate over partitions and extract disk drives
    for partition in partitions:
        # Extract drive name (e.g., 'C:', 'D:')
        drive_name = partition.device.split(':')[0]
        disk_drives.append(drive_name)

    return disk_drives

def verify_disk_drives(expected_disk_drives):
    actual_disk_drives = get_disk_drives()

    # Sort both lists for comparison
    expected_disk_drives.sort()
    actual_disk_drives.sort()

    # Verify that all expected disk drives are listed
    missing_drives = set(expected_disk_drives) - set(actual_disk_drives)
    if missing_drives:
        missing_drives_msg = ", ".join(missing_drives)
        logging.error("Disk drives verification failed. Missing drives: %s", missing_drives_msg)
        print("WARNING: The following expected disk drives are missing: {}".format(list(missing_drives)))
    else:
        logging.info("Disk drives verification successful. All expected disk drives are listed.")
        print("All expected disk drives are listed.")

# Example usage:
expected_disk_drives = ['C', 'D', 'E']  # Modify with your expected disk drives
verify_disk_drives(expected_disk_drives)


def test_disk_interface_operations(disk_drives):
    for drive in disk_drives:
        # Skip non-drive entries (e.g., drive identifiers)
        if 'sd' not in drive:  # Modify the condition as per your system's drive naming convention
            continue

        print("Performing interface operations on drive:", drive)

        # Add your specific test logic here
        try:
            # Measure start time
            start_time = time.time()

            # Write data to the disk drive
            with open(drive, 'wb') as file:
                # Write some dummy data (e.g., bytes)
                file.write(b'This is a test write operation.')

            # Measure end time after writing
            write_end_time = time.time()

            # Calculate transfer rate for write operation
            write_transfer_rate = os.path.getsize(drive) / (write_end_time - start_time)

            # Format the disk drive
            subprocess.run(['mkfs.ext4', drive])  # Example: format as ext4 filesystem, adjust as needed

            # Measure end time after formatting
            format_end_time = time.time()

            # Calculate latency for format operation
            format_latency = format_end_time - write_end_time

            # Assert that the disk is formatted successfully
            assert os.path.exists(drive), "Formatting operation failed for drive {}".format(drive)

            # Partition the disk drive
            subprocess.run(['fdisk', drive],
                           input=b'n\np\n\n\n\nw\n')  # Example: create a new partition, adjust as needed

            # Measure end time after partitioning
            partition_end_time = time.time()

            # Calculate latency for partition operation
            partition_latency = partition_end_time - format_end_time

            # Perform a read operation on the disk drive
            with open(drive, 'rb') as file:
                read_data = file.read()

            # Assert that the read data matches what was written
            assert read_data == b'This is a test write operation.', "Read operation failed for drive {}".format(drive)

            # Measure end time after reading
            read_end_time = time.time()

            # Calculate transfer rate for read operation
            read_transfer_rate = os.path.getsize(drive) / (read_end_time - partition_end_time)

            # Print performance metrics
            print("Performance Metrics:")
            print("Write Transfer Rate: {:.2f} bytes/sec".format(write_transfer_rate))
            print("Format Latency: {:.2f} seconds".format(format_latency))
            print("Partition Latency: {:.2f} seconds".format(partition_latency))
            print("Read Transfer Rate: {:.2f} bytes/sec".format(read_transfer_rate))

        except FileNotFoundError:
            assert False, "Drive {} not found".format(drive)
        except Exception as e:
            assert False, "Error performing operations on drive {}: {}".format(drive, e)

        print("Performance monitoring completed for drive", drive)


        # Publish test results to the dashboard
        def publish_to_dashboard(test_results):
            dashboard_url = 'http://127.0.0.1:5000'  # Update with your dashboard URL
            response = requests.post(dashboard_url, json=test_results)
            if response.status_code == 200:
                print("Test results published to dashboard successfully!")
            else:
                print("Failed to publish test results to dashboard.")

        if __name__ == "__main__":
            # Assuming you have obtained test results
            test_results = {
                'disk_interface_test': 'Pass'  # Example test result, replace with actual test result
            }
            publish_to_dashboard(test_results)
