# pull a base image which gives all required tools and libraries
FROM amazoncorretto:17-alpine

# create a folder where the app code will be shared
WORKDIR /app

# copy the source code from your HOST machine to your container
COPY src/Main.java /app/Main.java

# compile the application code
RUN javac Main.java

# run the application
CMD ["java", "Main"]
