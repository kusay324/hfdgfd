#include <httplib.h>
#include <iostream>
#include <fstream>
#include <sstream>

class QuranServer {
public:
    QuranServer() {
        svr.Get("/", [this](const httplib::Request& req, httplib::Response& res) {
            handleHomePage(req, res);
        });

        svr.Get("/app", [this](const httplib::Request& req, httplib::Response& res) {
            handleAppPage(req, res);
        });

        svr.Post("/submit_comment", [this](const httplib::Request& req, httplib::Response& res) {
            handleCommentSubmission(req, res);
        });

        std::cout << "Server is running on http://localhost:8080" << std::endl;
        svr.listen("localhost", 8080);
    }

private:
    void handleHomePage(const httplib::Request& req, httplib::Response& res) {
        std::string html_content = readHTMLFile("index.html");
        res.set_content(html_content, "text/html");
    }

    void handleAppPage(const httplib::Request& req, httplib::Response& res) {
        std::string html_content = readHTMLFile("app.html");
        res.set_content(html_content, "text/html");
    }

    void handleCommentSubmission(const httplib::Request& req, httplib::Response& res) {
        std::string comment = req.get_param_value("comment");
        if (!comment.empty()) {
            commentsSection.append("<p>" + comment + "</p>");
            res.set_content("تم إرسال التعليق بنجاح", "text/plain");
        } else {
            res.set_content("فشل إرسال التعليق", "text/plain");
        }
    }

    std::string readHTMLFile(const std::string& filename) {
        std::ifstream file(filename);
        if (file.is_open()) {
            std::stringstream buffer;
            buffer << file.rdbuf();
            return buffer.str();
        } else {
            return "Error: Could not open file";
        }
    }

private:
    httplib::Server svr;
    std::string commentsSection;
};

int main() {
    QuranServer server;
    return 0;
}
