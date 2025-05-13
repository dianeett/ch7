import mongoose from "mongoose";
import { MongoMemoryServer } from "mongodb-memory-server";
import Weather from "@/mongoose/weather/model"; // Correct the import based on the relative path
import { storeDocument } from "@/mongoose/weather/services"; // Update if needed, same logic applies

async function dbConnect(): Promise<void> {
  // Start an in-memory MongoDB server
  const mongoServer = await MongoMemoryServer.create();
  const MONGOIO_URI = mongoServer.getUri();

  // Disconnect from any existing MongoDB connection
  await mongoose.disconnect();

  // Establish a new connection to the in-memory MongoDB instance
  await mongoose.connect(MONGOIO_URI, {
    dbName: "Weather",
  });

  // Log successful connection
  mongoose.connection.on("connected", () => {
    console.log("Mongoose connected to in-memory MongoDB");
  });

  // Log any connection errors
  mongoose.connection.on("error", (err) => {
    console.error("Error connecting to MongoDB", err);
  });

  // Insert sample weather data into the database
  await storeDocument({
    zip: "96815",
    weather: "sunny",
    tempC: "25C",
    tempF: "70F",
    friends: ["96814", "96826"],
  });

  await storeDocument({
    zip: "96814",
    weather: "rainy",
    tempC: "20C",
    tempF: "68F",
    friends: ["96815", "96826"],
  });

  await storeDocument({
    zip: "12345",
    weather: "rainy",
    tempC: "30C",
    tempF: "86F",
    friends: ["96815", "96814"],
  });

  // Log the data after storing it to confirm it's in the database
  const weatherData = await Weather.find();  // Using Weather model here
  console.log("Inserted weather data:", weatherData);
}

export default dbConnect;
