Implemented a simple network (or rather, a distributed) file system from scratch on TCP sockets.

# Major Components
Clients: Clients represent the systems or users requesting access to files within the network file system. They serve as the primary interface to interact with the NFS, initiating various file-related operations such as reading, writing, deleting, and more.
Naming Server: The Naming Server stands as a pivotal component in the NFS architecture. This singular instance serves as a central hub, orchestrating communication between clients and the storage servers. Its primary function is to provide clients with crucial information about the specific storage server where a requested file or folder resides. Essentially, it acts as a directory service, ensuring efficient and accurate file access.
Storage Servers: Storage Servers form the foundation of the NFS. These servers are responsible for the physical storage and retrieval of files and folders. They manage data persistence and distribution across the network, ensuring that files are stored securely and efficiently.

Within the NFS ecosystem, clients enjoy a suite of essential file operations, enabling seamless interaction with the network file system:

Writing a File/Folder: Clients can actively create and update the content of files and folders within the NFS. This operation encompasses the storage and modification of data, ensuring that the NFS remains a dynamic repository.
Reading a File: Reading operations empower clients to retrieve the contents of files stored within the NFS. This fundamental operation grants clients access to the information they seek.
Deleting a File/Folder: Clients retain the ability to remove files and folders from the network file system when they are no longer needed, contributing to efficient space management.
Creating a File/Folder: The NFS allows clients to generate new files and folders, facilitating the expansion and organization of the file system. This operation involves the allocation of storage space and the initialization of metadata for the newly created entities.
Listing All Files and Folders in a Folder: Navigating the NFS structure becomes effortless for clients as they can retrieve comprehensive listings of files and subfolders within a specified directory. This feature aids in efficient file system exploration and management.
Getting Additional Information: Clients can access a wealth of supplementary information about specific files. This includes details such as file size, access rights, timestamps, and other metadata, providing clients with comprehensive insights into the files they interact with

# Specifications
1. Naming and Storage Servers
1.1 INITIALIZATION 
The process of initializing the Naming Server (NM) and Storage Servers (SS) sets the foundation for the entire network file system. Here are the key steps involved in starting the system:

Initialize the Naming Server (NM): The first step is to initialize the Naming Server, which serves as the central coordination point in the NFS. It is responsible for managing the directory structure and maintaining essential information about file locations.
Initialize Storage Server 1 (SS_1): Each Storage Server (SS) is responsible for storing files and interacting with both the Naming Server and clients. Initialization of SS_1 involves several sub-steps:
Upon initialization, SS_1 sends vital details about its existence to the Naming Server. This information includes:
IP address: To facilitate communication and location tracking.
Port for NM Connection: A dedicated port for direct communication with the Naming Server.
Port for Client Connection: A separate port for clients to interact with SS_1.
List of Accessible Paths: A comprehensive list of file and folder paths that are accessible on SS_1.
Initialize SS_2 to SS_n: Following the same procedure as SS_1, additional Storage Servers (SS_2 to SS_n) are initialized, each providing their details to the Naming Server.
NM Starts Accepting Client Requests: Once all Storage Servers are initialized and registered with the Naming Server, the Naming Server begins accepting client requests. It becomes the central point through which clients access and manage files and directories within the network file system.
1.2 ON STORAGE SERVERS (SS) 
The Storage Servers play a crucial role in the network file system and are equipped with the following functionalities:

Adding new storage servers: New Storage Servers (i.e., which begin running after the initial initialization phase) have the capability to dynamically add their entries to the Naming Server at any point during execution. This flexibility ensures that the system can adapt to changes and scaling requirements seamlessly.
Commands Issued by NM: The Naming Server can issue specific commands to the Storage Servers, including:
Create an Empty File/Directory: The Naming Server can instruct a Storage Server to create an empty file or directory, initiating the storage of new data.
Delete a File/Directory: Storage Servers can receive commands to delete files or directories, thereby freeing up storage space and maintaining data consistency.
Copy Files/Directories: Storage Servers can copy files or directories from other Storage Servers, with the NM providing the relevant IP addresses for efficient data transfer.
Client Interactions: Storage Servers also facilitate client interactions by providing the following functionalities:
Read a File: Clients can request Storage Servers to retrieve and send the content of a specific file, allowing seamless data access.
Write to a File: Clients can send data to Storage Servers, which is then written to the designated file. This operation ensures data updates and modifications are correctly stored.
Get Size and Permissions: Clients can query Storage Servers to retrieve essential file information such as size and access permissions, aiding in file management.
1.3 ON NAMING SERVER (NM) 
Storing Storage Server data: One of the fundamental functions of the Naming Server (NM) is to serve as the central repository for critical information provided by Storage Servers (SS) upon connection.
Client task feedback: Upon completion of tasks initiated by clients (as described in Specification 2), the NM plays a pivotal role in providing timely and relevant feedback to the requesting clients.

2. Clients
Clients initiate communication with the Naming Server (NM) to interact with the NFS. Here’s how this interaction unfolds:

Directory Mounting: Say, the client passes READ dir1/dir2/file.txt to the NM -> the NM looks over all the accessible paths in SS1, SS2, SS3… then sees that the path is present in SSx -> The NM gives relevant information about SSx to the client.
Functionalities to implement are:
Reading, Writing, and Retrieving Information about Files:
Client Request: Clients can initiate file-related operations such as reading, writing, or obtaining information by providing the file’s path. The NM, upon receiving the request, takes on the responsibility of locating the correct SS where the file is stored.
NM Facilitation: The NM identifies the correct Storage Server and returns the precise IP address and client port for that SS to the client.
Direct Communication: Subsequently, the client directly communicates with the designated SS. This direct communication is established, and the client continuously receives information packets from the SS until a predefined “STOP” packet is sent or a specified condition for task completion is met. The “STOP” packet serves as a signal to conclude the operation.
Creating and Deleting Files and Folders:
Client Request: Clients can request file and folder creation or deletion operations by providing the respective path and action (create or delete). Once the NM determines the correct SS, it forwards the request to the appropriate SS for execution.
SS Execution: The SS processes the request and performs the specified action, such as creating an empty file or deleting a file/folder.
Acknowledgment and Feedback: After successful execution, the SS sends an acknowledgment (ACK) or a STOP packet to the NM to confirm task completion. The NM, in turn, conveys this information back to the client, providing feedback on the task’s status.
Copying Files/Directories Between Storage Servers:
Client Request: Clients can request to copy files or directories between SS by providing both the source and destination paths. The NM, upon receiving this request, assumes responsibility for managing the file/folder transfer between the relevant SS.
NM Execution: The NM orchestrates the copying process, ensuring that data is transferred accurately. This operation may involve copying data from one SS to another, even if both source and destination paths lie within the same SS.
Acknowledgment and Client Notification: Upon the successful completion of the copy operation, the NM sends an acknowledgment (ACK) packet to the client. This ACK serves as confirmation that the requested data transfer has been successfully executed.
NOTE: Marks awarded here are essentially for the requests to the NM. Actual implementation of the requests by the NM and SSs have been assigned points in Specification 1.

3. Other features

3.1 MULTIPLE CLIENTS 
Concurrent Client Access: Your NFS design accommodates multiple clients attempting to access the Naming Server (NM) simultaneously. To ensure a smooth experience, the NM responds to client requests with an initial ACK to acknowledge the receipt of the request. If this initial ACK is not received within a reasonable timeframe, the client may display a timeout message. Importantly, the NM should not block while processing operations specified in parts 2 and 3 of Specification 2. To achieve this, an initial ACK and a final ACK from the relevant Storage Server (SS) can be employed, allowing the NM to handle other requests between these acknowledgments.
Concurrent File Reading: While multiple clients can read the same file simultaneously, only one client can execute write operations on a file at any given time.
3.2 ERROR CODES 
Error Handling: Define a set of error codes that can be returned when a client’s request cannot be accommodated. For example, the NM should return distinct error codes for situations where a file is unavailable because it doesn’t exist and when another client is currently writing to the file. Clear and descriptive error codes enhance the communication of issues between the NFS and clients.
3.3 SEARCH IN NAMING SERVERS 
Efficient Search: Optimize the search process employed by the Naming Server when serving client requests. Avoid linear searches and explore more efficient data structures such as Tries to swiftly identify the correct Storage Server (SS) for a given request. This optimization enhances response times, especially in systems with a large number of files and folders.
LRU Caching: Implement LRU (Least Recently Used) caching for recent searches. By caching recently accessed information, the NM can expedite subsequent requests for the same data, further improving response times and system efficiency.
3.4 BOOKKEEPING 
Logging and Message Display: Implement a logging mechanism where the NM records every request or acknowledgment received from clients or Storage Servers. Additionally, the NM should display or print relevant messages indicating the status and outcome of each operation. This bookkeeping ensures traceability and aids in debugging and system monitoring.
IP Address and Port Recording: The log should include relevant information such as IP addresses and ports used in each communication, enhancing the ability to trace and diagnose issues.