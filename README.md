# TDC-Assignment
This repository has the implementation logic for building a distributed system for image processing

Sai Krishna Kaushik Garlapati
ID - 029336162

Implement a Distributed System for Image Processing

Assignment: 
In this assignment, you will implement a distributed system for processing images in parallel. The system will consist of a number of worker nodes, each of which will be responsible for processing a portion of the input image.
To begin, you will need to choose a programming language and a communication protocol for the worker nodes to use to communicate with each other. You may choose any language and protocol you wish, but it should be one that is well-suited to distributed computing.
Once you have chosen a language and a protocol, you will need to implement the following components:
1.	A master node that is responsible for dividing the input image into smaller chunks and distributing them to the worker nodes. The master node should also be responsible for collecting the processed chunks from the worker nodes and reassembling them into a single output image.
2.	A worker node that is responsible for receiving a chunk of the input image from the master node, processing it, and returning the processed chunk to the master node. The processing step can be anything you wish - for example, you could implement a simple image filtering algorithm or something more complex like facial recognition.
3.	A simple client program that can be used to submit an input image to the master node for processing. The client program should be able to display the output image once it has been processed by the worker nodes.

Required Tools:-
Docker
Docker Compose
Python

Example Code 
Master Node -

class MasterNode:
    def __init__(self, num_worker_nodes):
        self.num_worker_nodes = num_worker_nodes
        self.comm = CommunicationProtocol()  # Choose a communication protocol here
    
    def process_image(self, input_image_path):
        # Load the input image
        input_image = Image.open(input_image_path)
        width, height = input_image.size
        
        # Divide the input image into smaller chunks
        chunk_width = width // self.num_worker_nodes
        chunk_height = height
        input_chunks = []
        for i in range(self.num_worker_nodes):
            chunk = input_image.crop((i * chunk_width, 0, (i + 1) * chunk_width, chunk_height))
            input_chunks.append(chunk)
        
        # Distribute the chunks to the worker nodes
        for i, chunk in enumerate(input_chunks):
            self.comm.send(i, chunk)
        
        # Collect the processed chunks from the worker nodes
        output_chunks = []
        for i in range(self.num_worker_nodes):
            chunk = self.comm.receive(i)
            output_chunks.append(chunk)
        
        # Reassemble the output chunks into a single image
        output_image = Image.new('RGB', (width, height))
        for i, chunk in enumerate(output_chunks):
            output_image.paste(chunk, (i * chunk_width, 0))
        
        return output_image

Worker node ➖
class WorkerNode:
    def __init__(self):
        self.comm = CommunicationProtocol()  # Choose a communication protocol here
    
    def process_chunk(self):
        # Receive a chunk of the input image from the master node
        chunk = self.comm.receive()
        
        # Convert the image to grayscale and apply the Sobel edge detection filter
        img_array = np.array(chunk)
        img_array = np.mean(img_array, axis=2)  # Convert to grayscale
        img_array = sobel(img_array)
        
        # Convert the image back to a PIL Image object and return it
        processed_chunk = Image.fromarray(img_array)
        return processed_chunk

Client code ➖

// client code
import sys
from PIL import Image

if __name__ == '__main__':
    if len(sys.argv) != 3:
        print('Usage: python client.py <input_image> <num_worker_nodes>')
        sys.exit(1)
    
    input_image_path = sys.argv[1]
    num_worker_nodes = int(sys.argv[2])
    
    master_node = MasterNode(num_worker_nodes)
    output_image = master_node.process_image(input_image_path)
    output_image.show()


Deliverables
You should measure the time it takes to process the entire image, and compare it to the time it would take to process the image on a single node.
Once you have implemented and tested your system, you should write a short report discussing your design choices, the challenges you faced, and any improvements you would make if you were to do the project again.


