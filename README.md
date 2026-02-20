export default {
  async fetch(request) {
    try {
      const url = new URL(request.url);
      const num = url.searchParams.get("num");

      // Validate parameter
      if (!num) {
        return new Response(JSON.stringify({
          status: false,
          message: "Missing required parameter: num"
        }, null, 2), {
          status: 400,
          headers: { "Content-Type": "application/json" }
        });
      }

      // Original API
      const sourceURL = `https://num-to-info.alphamovies.workers.dev/api/numinfo?key=support-kro-sb&num=${num}`;

      const response = await fetch(sourceURL);

      if (!response.ok) {
        return new Response(JSON.stringify({
          status: false,
          message: "Failed to fetch data from source API"
        }, null, 2), {
          status: 502,
          headers: { "Content-Type": "application/json" }
        });
      }

      const data = await response.json();

      // Safe details object
      const details = data.details || {};

      // Custom formatted response
      const finalResponse = {
        status: true,
        details: {
          "Person name": details["aadhaar Owner name"] || "Not Available",
          "Father_name": details["sim number father name"] || "Not Available",
          "Address": details["Address"] || "Not Available",
          "City Name": details["City Name"] || "Not Available",
          "Dob": details["dob"] || "Not Available",
          "Created": "shayan_explorer"
        },
        api_info: "api created by SHAYAN EXPLORER"
      };

      return new Response(JSON.stringify(finalResponse, null, 2), {
        headers: {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*"
        }
      });

    } catch (error) {
      return new Response(JSON.stringify({
        status: false,
        message: "Internal Server Error",
        error: error.message
      }, null, 2), {
        status: 500,
        headers: { "Content-Type": "application/json" }
      });
    }
  }
};
