# Machine Learning + Kafka Streams Examples

General info in main [Readme](../readme.md) 

### Example 2 - Convolutional Neural Network (CNN) with TensorFlow for Image Recognition
**Use Case**

Convolutional Neural Network (CNN) to for image recognition.
A prebuilt TensorFlow CNN model is instantiated and used in a Kafka Streams application to do recognize new JPEG images. A Kafka Input Topic receives the location of a new images (another option would be to send the image in the Kafka message instead of just a link to it), infers the content of the picture via the TensorFlow model, and sends the result to a Kafka Output Topic.

**Machine Learning Technology**
* [TensorFlow](https://www.tensorflow.org/)
* Leverages [TensorFlow for Java](https://www.tensorflow.org/install/install_java). These APIs are particularly well-suited for loading models created in Python and executing them within a Java application. Please note: The Java API doesn't yet include convenience functions (which you might know from [Keras](https://keras.io/)), thus a private helper class is used in the example for construction and execution of the pre-built TensorFlow model.
* Check the official TensorFlow demo [LabelImage](https://github.com/kaiwaehner/tensorflow/blob/r1.3/tensorflow/java/src/main/java/org/tensorflow/examples/LabelImage.java) to understand this image recognition example
* You can re-use the pre-trained TensorFlow model attached to this project [tensorflow_inception_graph.pb](http://arxiv.org/abs/1512.00567) or add your own model.
* The 'images' folder contains models which were used for training the model (trained_airplane_1.jpg, trained_airplane_2.jpg, trained_butterfly.jpg) but also a new picture (new_airplane.jpg) which is not known by the model and using a different resolution than the others. Feel free to add your own pictures (they need to be trained, see list of trained pictures in the file: imagenet_comp_graph_label_strings.txt), otherwise the model will return 'unknown'.

**Source Code**

[Kafka_Streams_TensorFlow_Image_Recognition_Example.java](src/main/java/com/github/megachucky/kafka/streams/machinelearning/Kafka_Streams_TensorFlow_Image_Recognition_Example.java)

**Unit Test**

[Kafka_Streams_TensorFlow_Image_Recognition_ExampleTest.java](src/test/java/com/github/megachucky/kafka/streams/machinelearning/Kafka_Streams_TensorFlow_Image_Recognition_ExampleTest.java)
[Kafka_Streams_TensorFlow_Image_Recognition_Example_IntegrationTest.java](src/test/java/com/github/megachucky/kafka/streams/machinelearning/test/Kafka_Streams_TensorFlow_Image_Recognition_Example_IntegrationTest.java)

## **To Run the project**

  # packaging jar file

	mvn clean package

# running project (in a terminal)

	java -cp target/tensorflow-image-recognition-CP53_AK23-jar-with-dependencies.jar com.github.megachucky.kafka.streams.machinelearning.Kafka_Streams_TensorFlow_Image_Recognition_Example

# Run GRPC-TensorFlow-Server docker container

	docker run -it -p 9000:9000 tgowda/inception_serving_tika

	Inside the container, start the Tensorflow Serving server - this deploys the TensorFlow model for Image Recognition

	root@8311ea4e8074:/# /serving/server.sh

# Run Confluent platform (Kafka + Zookeeper)

	confluent local start

# CREATE necessary topics for  

	kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic ImageInputTopic

	kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic ImageOutputTopic

# push some pictures to ImageInputTopic


	echo -e "/Users/aironman/gitProjects/kafka-streams-machine-learning-examples/tensorflow-image-recognition/src/main/resources/TensorFlow_Images/trained_airplane_1.jpg" | kafkacat -b localhost:9092 -P -t ImageInputTopic


	echo -e "/Users/aironman/gitProjects/kafka-streams-machine-learning-examples/tensorflow-image-recognition/src/main/resources/TensorFlow_Images/trained_airplane_2.jpg" | kafkacat -b localhost:9092 -P -t ImageInputTopic


	echo -e "/Users/aironman/gitProjects/kafka-streams-machine-learning-examples/tensorflow-image-recognition/src/main/resources/TensorFlow_Images/trained_butterfly.jpg" | kafkacat -b localhost:9092 -P -t ImageInputTopic

	echo -e "/Users/aironman/gitProjects/kafka-streams-machine-learning-examples/tensorflow-image-recognition/src/main/resources/TensorFlow_Images/new_airplane.jpg" | kafkacat -b localhost:9092 -P -t ImageInputTopic

# consume predictions...

    kafka-console-consumer --bootstrap-server localhost:9092 --topic ImageOutputTopic --from-beginning


# or, in the running jar file terminal...

	Last login: Tue Nov 19 12:25:55 on ttys006
	aironman@MacBook-Pro-de-Alonso tensorflow-image-recognition % fish
	Welcome to fish, the friendly interactive shell
	aironman@MacBook-Pro-de-Alonso ~/g/k/tensorflow-image-recognition> java -cp target/tensorflow-image-recognition-CP53_AK23-jar-with-dependencies.jar com.github.megachucky.kafka.streams.machinelearning.Kafka_Streams_TensorFlow_Image_Recognition_Example
	                                                                   
	SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
	SLF4J: Defaulting to no-operation (NOP) logger implementation
	SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
	Image Recognition Microservice is running...
	Input to Kafka Topic ImageInputTopic; Output to Kafka Topic ImageOutputTopic
	2019-11-19 12:37:44.005196: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.
	2019-11-19 12:37:44.005228: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX instructions, but these are available on your machine and could speed up CPU computations.
	2019-11-19 12:37:44.005233: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX2 instructions, but these are available on your machine and could speed up CPU computations.
	2019-11-19 12:37:44.005236: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use FMA instructions, but these are available on your machine and could speed up CPU computations.
	BEST MATCH: space shuttle (47,19% likely)
	BEST MATCH: airliner (63,22% likely)
	BEST MATCH: cabbage butterfly (26,23% likely)
	BEST MATCH: airliner (54,36% likely)


