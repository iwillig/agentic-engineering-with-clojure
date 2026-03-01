Agentic Browser with Etaoin
============================

https://github.com/clj-commons/etaoin

Overview
--------

Etaoin is a pure Clojure WebDriver client library that enables browser
automation through a simple, functional API. It supports multiple
browsers (Firefox, Chrome, Edge, Safari) and provides a comprehensive
set of operations for navigation, element interaction, JavaScript
execution, and page inspection.

By combining clj-nrepl-eval and etaoin, we have a powerful agentic
browser that can be used to verify web applications. It is then a
small step to take your explorations on the REPL and translate them
into a TEST.

Installation
------------

Add Etaoin to your project using ``clojure.repl.deps``:

.. code-block:: clojure

   (require '[clojure.repl.deps :refer [add-libs]])
   (add-libs '{etaoin/etaoin {:mvn/version "1.1.42"}})
   (require '[etaoin.api :as e])

Core Workflow Pattern
---------------------

Every browser automation follows this pattern:

1. **Launch** - Start a browser driver session
2. **Navigate** - Open URLs
3. **Query** - Find elements on the page
4. **Interact** - Click, fill, select, etc.
5. **Extract** - Get text, attributes, page state
6. **Wait** - Synchronize with dynamic content
7. **Close** - Clean up the driver session

Basic Firefox Session
----------------------

.. code-block:: clojure

   ;; Launch Firefox
   (def driver (e/firefox))

   ;; Navigate to a page
   (e/go driver "https://example.com")

   ;; Get page information
   (e/get-title driver)
   ;; => "Example Domain"

   (e/get-url driver)
   ;; => "https://example.com"

   ;; Clean up
   (e/quit driver)

Using with-driver for Automatic Cleanup
----------------------------------------

The ``with-driver`` macro automatically launches and terminates the browser, even if exceptions occur:

.. code-block:: clojure

   (e/with-driver :firefox driver
     (e/go driver "https://example.com")
     (e/get-title driver))
   ;; Driver automatically closed after body executes

Query API - Finding Elements
-----------------------------

Etaoin provides flexible element querying:

**By ID (keyword)**

.. code-block:: clojure

   (e/query driver :email-input)
   ;; Finds element with id="email-input"

**By CSS selector**

.. code-block:: clojure

   (e/query driver {:css "input.email"})
   (e/query driver {:css "button[type='submit']"})

**By XPath**

.. code-block:: clojure

   (e/query driver {:xpath "//input[@name='email']"})
   (e/query driver ".//button[contains(text(),'Submit')]")

**By tag and attributes**

.. code-block:: clojure

   (e/query driver {:tag :input :name "email"})
   (e/query driver {:tag :button :type "submit"})

**Nested queries (DOM traversal)**

.. code-block:: clojure

   ;; Find input inside a specific div
   (e/query driver
            {:tag :div :class "form-section"}
            {:tag :input :name "email"})

**Query all matching elements**

.. code-block:: clojure

   (e/query-all driver {:tag :a})
   ;; => ["element-id-1" "element-id-2" "element-id-3"]

**Query tree (hierarchical search)**

.. code-block:: clojure

   ;; Find all links within all divs
   (e/query-tree driver {:tag :div} {:tag :a})

Interaction - Clicking and Typing
----------------------------------

**Clicking elements**

.. code-block:: clojure

   ;; Click by query
   (e/click driver {:tag :button :type "submit"})
   (e/click driver :login-button)

   ;; Double click
   (e/double-click driver :menu-item)

   ;; Right click
   (e/right-click-on driver :context-menu-trigger)

**Filling forms**

.. code-block:: clojure

   ;; Clear and fill input
   (e/fill driver :email "user@example.com")

   ;; Fill multiple inputs (map - no order guarantee)
   (e/fill-multi driver {:email "user@example.com"
                         :password "secret123"})

   ;; Fill multiple inputs in order (vector)
   (e/fill-multi driver [:email "user@example.com"
                         :password "secret123"])

   ;; Type without clearing (append)
   (e/fill driver :search "hello")
   (e/fill driver :search " world")  ;; Now contains "hello world"

   ;; Human-like typing with random delays
   (e/fill-human driver :comment "This looks more natural"
                 {:mistake-prob 0.1 :delay-ms [50 200]})

**Keyboard operations**

.. code-block:: clojure

   ;; Press keys
   (e/press driver :enter)
   (e/press driver :tab)
   (e/press driver :escape)

   ;; Clear an input
   (e/clear driver :email)

**Selecting from dropdowns**

.. code-block:: clojure

   ;; Select by visible text
   (e/select driver :country "United States")

**Checkboxes and radio buttons**

.. code-block:: clojure

   ;; Check a checkbox
   (e/click driver :terms-checkbox)

   ;; Check if selected
   (e/selected? driver :terms-checkbox)
   ;; => true

Data Extraction
---------------

**Getting element text**

.. code-block:: clojure

   (e/get-element-text driver {:tag :h1})
   ;; => "Welcome to the Page"

   ;; Get text from all matching elements
   (def links (e/query-all driver {:tag :a}))
   (map #(e/get-element-text-el driver %) links)

**Getting attributes**

.. code-block:: clojure

   (e/get-element-attr driver :email "placeholder")
   ;; => "Enter your email"

   ;; Get multiple attributes at once
   (e/get-element-attrs driver :submit-btn "class" "id" "type")
   ;; => ["btn btn-primary" "submit-btn" "submit"]

**Getting element properties**

.. code-block:: clojure

   (e/get-element-value driver :email)
   ;; => "user@example.com" (current value in input)

   (e/get-element-property driver :checkbox "checked")
   ;; => true or false

**Getting CSS styles**

.. code-block:: clojure

   (e/get-element-css driver :banner "background-color")
   ;; => "rgb(255, 0, 0)"

**Getting page source**

.. code-block:: clojure

   (e/get-source driver)
   ;; => "<html>...</html>" (full page HTML)

**Getting element location and size**

.. code-block:: clojure

   (e/get-element-location driver :banner)
   ;; => {:x 100 :y 50}

   (e/get-element-size driver :banner)
   ;; => {:width 800 :height 200}

   (e/get-element-rect driver :banner)
   ;; => {:x 100 :y 50 :width 800 :height 200}

Waiting and Synchronization
----------------------------

**Wait for element to exist**

.. code-block:: clojure

   (e/wait-exists driver {:tag :div :class "content"})

**Wait for visibility**

.. code-block:: clojure

   (e/wait-visible driver :modal)
   (e/wait-invisible driver :loading-spinner)

**Wait for element to be absent**

.. code-block:: clojure

   (e/wait-absent driver :error-message)

**Wait for element state**

.. code-block:: clojure

   (e/wait-enabled driver :submit-button)
   (e/wait-disabled driver :submit-button)

**Wait for text**

.. code-block:: clojure

   (e/wait-has-text driver :status "Complete")

**Wait with custom predicate**

.. code-block:: clojure

   (e/wait-predicate
     (fn []
       (= "Ready" (e/get-element-text driver :status)))
     {:timeout 10 :interval 0.5})

**Simple time-based wait**

.. code-block:: clojure

   (e/wait driver 2)  ;; Wait 2 seconds

**Custom wait intervals and timeouts**

.. code-block:: clojure

   (e/with-wait-interval 0.5  ;; Check every 0.5 seconds
     (e/with-wait-timeout 30   ;; Timeout after 30 seconds
       (e/wait-visible driver :slow-element)))

JavaScript Execution
--------------------

**Execute synchronous JavaScript**

.. code-block:: clojure

   ;; Simple expression
   (e/js-execute driver "return document.title;")
   ;; => "Page Title"

   ;; With arguments
   (e/js-execute driver "return arguments[0] + arguments[1];" 5 10)
   ;; => 15

   ;; Query DOM
   (e/js-execute driver
     "return document.querySelectorAll('img').length;")
   ;; => 42

   ;; Modify page
   (e/js-execute driver
     "document.body.style.backgroundColor = 'lightblue';")

**Execute async JavaScript**

.. code-block:: clojure

   (e/js-async driver
     "const callback = arguments[arguments.length - 1];
      setTimeout(() => callback('done'), 1000);"
     {:timeout 5})

**Local storage operations**

.. code-block:: clojure

   (e/js-execute driver
     "localStorage.setItem('key', 'value');")

   (e/js-execute driver
     "return localStorage.getItem('key');")
   ;; => "value"

   (e/js-localstorage-clear driver)

Navigation and Window Management
---------------------------------

**Navigation**

.. code-block:: clojure

   ;; Go to URL
   (e/go driver "https://example.com")

   ;; Navigate back
   (e/back driver)

   ;; Navigate forward
   (e/forward driver)

   ;; Refresh page
   (e/refresh driver)

**Working with windows and tabs**

.. code-block:: clojure

   ;; Get current window handle
   (e/get-window-handle driver)

   ;; Get all window handles
   (e/get-window-handles driver)
   ;; => ["window-1" "window-2"]

   ;; Switch to window
   (e/switch-window driver "window-2")

   ;; Switch to next window
   (e/switch-window-next driver)

   ;; Close current window
   (e/close-window driver)

**Working with frames**

.. code-block:: clojure

   ;; Switch to frame by index
   (e/switch-frame driver 0)

   ;; Switch to frame by element
   (e/switch-frame driver {:tag :iframe :id "content-frame"})

   ;; Switch to parent frame
   (e/switch-frame-parent driver)

   ;; Switch to top frame
   (e/switch-frame-top driver)

   ;; Use with-frame for automatic frame switching
   (e/with-frame driver {:tag :iframe :id "payment"}
     (e/fill driver :card-number "4111111111111111"))
   ;; Automatically returns to previous frame

Scrolling
---------

.. code-block:: clojure

   ;; Scroll to element
   (e/scroll-query driver :footer)

   ;; Scroll by offset
   (e/scroll-by driver 0 500)  ;; Scroll down 500px

   ;; Scroll to top
   (e/scroll-top driver)

   ;; Scroll to bottom
   (e/scroll-bottom driver)

   ;; Scroll in specific direction
   (e/scroll-down driver 300)
   (e/scroll-up driver 200)
   (e/scroll-left driver 100)
   (e/scroll-right driver 100)

Cookies
-------

.. code-block:: clojure

   ;; Get all cookies
   (e/get-cookies driver)
   ;; => [{:name "session" :value "abc123" :domain "example.com" ...}]

   ;; Get specific cookie
   (e/get-cookie driver "session")
   ;; => {:name "session" :value "abc123" ...}

   ;; Set cookie
   (e/set-cookie driver {:name "preference"
                         :value "dark-mode"
                         :path "/"})

   ;; Delete cookie
   (e/delete-cookie driver "session")

   ;; Delete all cookies
   (e/delete-cookies driver)

Screenshots and Visual Capture
-------------------------------

.. code-block:: clojure

   ;; Take screenshot
   (e/screenshot driver "page.png")

   ;; Screenshot specific element
   (e/screenshot-element driver :banner "banner.png")

   ;; Screenshots are automatically saved to files
   ;; File path can be relative or absolute
   (e/screenshot driver "/tmp/screenshots/test-001.png")

Alerts and Dialogs
------------------

.. code-block:: clojure

   ;; Check if alert is present
   (e/has-alert? driver)
   ;; => true or false

   ;; Get alert text
   (e/get-alert-text driver)
   ;; => "Are you sure?"

   ;; Accept alert (click OK)
   (e/accept-alert driver)

   ;; Dismiss alert (click Cancel)
   (e/dismiss-alert driver)

   ;; Wait for alert
   (e/wait-has-alert driver)

Element State Checking
-----------------------

.. code-block:: clojure

   ;; Check if element exists
   (e/exists? driver :submit-button)
   ;; => true or false

   ;; Check if element is visible
   (e/visible? driver :modal)
   ;; => true or false

   ;; Check if element is invisible
   (e/invisible? driver :loading)
   ;; => true or false

   ;; Check if element is enabled
   (e/enabled? driver :submit-button)
   ;; => true or false

   ;; Check if element is disabled
   (e/disabled? driver :submit-button)
   ;; => true or false

   ;; Check if element is selected
   (e/selected? driver :checkbox)
   ;; => true or false

   ;; Check if element has class
   (e/has-class? driver :button "active")
   ;; => true or false

   ;; Check if element has text
   (e/has-text? driver :message "Success")
   ;; => true or false

Agentic Browser Patterns
-------------------------

**Pattern 1: Page Inspection and Data Extraction**

.. code-block:: clojure

   (defn extract-article-metadata [driver url]
     (e/go driver url)
     (e/wait-visible driver {:tag :article})
     {:title (e/get-element-text driver {:tag :h1})
      :author (e/get-element-text driver {:class "author"})
      :date (e/get-element-text driver {:class "publish-date"})
      :content (e/get-element-text driver {:tag :article})
      :images (map #(e/get-element-attr-el driver % "src")
                   (e/query-all driver {:tag :img}))})

**Pattern 2: Form Automation**

.. code-block:: clojure

   (defn submit-contact-form [driver {:keys [name email message]}]
     (e/go driver "https://example.com/contact")
     (e/wait-visible driver :contact-form)
     (e/fill-multi driver [:name name
                           :email email
                           :message message])
     (e/click driver {:tag :button :type "submit"})
     (e/wait-visible driver :success-message)
     (e/get-element-text driver :success-message))

**Pattern 3: Authentication and Session Management**

.. code-block:: clojure

   (defn login [driver username password]
     (e/go driver "https://app.example.com/login")
     (e/wait-visible driver :login-form)
     (e/fill driver :email username)
     (e/fill driver :password password)
     (e/click driver :login-button)
     (e/wait-visible driver :dashboard)
     ;; Save cookies for later
     (e/get-cookies driver))

   (defn restore-session [driver cookies]
     (e/go driver "https://app.example.com")
     (doseq [cookie cookies]
       (e/set-cookie driver cookie))
     (e/refresh driver)
     (e/wait-visible driver :dashboard))

**Pattern 4: Dynamic Content Monitoring**

.. code-block:: clojure

   (defn wait-for-price-change [driver target-price]
     (e/wait-predicate
       (fn []
         (let [current-price-text (e/get-element-text driver :price)
               current-price (parse-double
                               (clojure.string/replace
                                 current-price-text #"[^0-9.]" ""))]
           (<= current-price target-price)))
       {:timeout 300 :interval 5}))

**Pattern 5: Multi-Page Navigation and Scraping**

.. code-block:: clojure

   (defn scrape-paginated-results [driver base-url]
     (loop [page 1
            results []]
       (e/go driver (str base-url "?page=" page))
       (e/wait-visible driver :results-container)
       (let [items (e/query-all driver {:class "result-item"})
             page-results (map #(e/get-element-text-el driver %) items)
             has-next? (e/exists? driver :next-page-button)]
         (if has-next?
           (recur (inc page) (into results page-results))
           (into results page-results)))))

**Pattern 6: Error Recovery and Retry Logic**

.. code-block:: clojure

   (defn robust-click [driver query max-retries]
     (loop [attempt 1]
       (try
         (e/wait-visible driver query {:timeout 5})
         (e/click driver query)
         :success
         (catch Exception e
           (if (< attempt max-retries)
             (do
               (e/refresh driver)
               (e/wait driver 2)
               (recur (inc attempt)))
             (throw e))))))

**Pattern 7: Visual Verification with Screenshots**

.. code-block:: clojure

   (defn verify-page-state [driver state-name]
     (let [screenshot-path (str "screenshots/" state-name ".png")]
       (e/screenshot driver screenshot-path)
       {:state state-name
        :url (e/get-url driver)
        :title (e/get-title driver)
        :screenshot screenshot-path
        :timestamp (java.time.Instant/now)}))

**Pattern 8: Table Data Extraction**

.. code-block:: clojure

   (defn extract-table-data [driver table-query]
     (let [headers (e/query-all driver table-query {:tag :th})
           header-texts (map #(e/get-element-text-el driver %) headers)
           rows (e/query-all driver table-query {:tag :tbody} {:tag :tr})]
       (for [row rows]
         (let [cells (e/query-all-from driver row {:tag :td})
               cell-texts (map #(e/get-element-text-el driver %) cells)]
           (zipmap header-texts cell-texts)))))

**Pattern 9: Conditional Actions Based on Page State**

.. code-block:: clojure

   (defn handle-modal-if-present [driver]
     (when (e/exists? driver :cookie-consent-modal)
       (e/click driver :accept-cookies))
     (when (e/exists? driver :newsletter-popup)
       (e/click driver :close-popup)))

**Pattern 10: Download File Handling**

.. code-block:: clojure

   (defn download-report [driver report-type download-dir]
     (e/go driver "https://app.example.com/reports")
     (e/wait-visible driver :report-selector)
     (e/select driver :report-type report-type)
     (e/click driver :download-button)
     ;; Wait for download to complete
     (e/wait driver 5)
     ;; Return path to downloaded file
     (str download-dir "/" report-type ".pdf"))

Driver Options and Configuration
---------------------------------

**Headless mode**

.. code-block:: clojure

   (def driver (e/firefox-headless))
   ;; Or with chrome
   (def driver (e/chrome-headless))

**Custom options**

.. code-block:: clojure

   (def driver
     (e/firefox
       {:args ["--window-size=1920,1080"
               "--disable-gpu"]
        :prefs {:download.default_directory "/tmp/downloads"}}))

**Custom executable path**

.. code-block:: clojure

   (def driver
     (e/firefox
       {:path-driver "/usr/local/bin/geckodriver"
        :path-browser "/Applications/Firefox.app/Contents/MacOS/firefox"}))

**Set timeouts**

.. code-block:: clojure

   (e/set-page-load-timeout driver 30)
   (e/set-script-timeout driver 10)
   (e/set-implicit-timeout driver 5)

**Window size and position**

.. code-block:: clojure

   (e/set-window-size driver 1920 1080)
   (e/set-window-position driver 0 0)
   (e/maximize driver)

**Get browser status**

.. code-block:: clojure

   (e/get-status driver)
   ;; Returns browser capabilities and status

   (e/running? driver)
   ;; => true or false

Browser-Specific Helpers
-------------------------

.. code-block:: clojure

   ;; Check browser type
   (e/firefox? driver)  ;; => true
   (e/chrome? driver)   ;; => false

   ;; Execute code only for specific browsers
   (e/when-firefox driver
     (println "Running on Firefox"))

   (e/when-chrome driver
     (println "Running on Chrome"))

Complete Agentic Browser Example
---------------------------------

.. code-block:: clojure

   (defn autonomous-research-agent
     "Research a topic by searching and extracting information"
     [topic]
     (e/with-driver :firefox driver
       ;; Search for the topic
       (e/go driver "https://www.google.com")
       (e/wait-visible driver {:name "q"})
       (e/fill driver {:name "q"} topic)
       (e/fill driver {:name "q"} e/keys-enter)
       (e/wait-visible driver {:id "search"})

       ;; Extract top results
       (let [result-links (take 5 (e/query-all driver {:css "h3"}))]
         (for [link-el result-links]
           (let [link-text (e/get-element-text-el driver link-el)
                 ;; Click to visit page
                 _ (e/click-el driver link-el)
                 _ (e/wait driver 2)

                 ;; Extract page content
                 title (e/get-title driver)
                 url (e/get-url driver)
                 content (e/get-element-text driver {:tag :body})

                 ;; Take screenshot for evidence
                 screenshot-path (str "research-"
                                     (hash url)
                                     ".png")
                 _ (e/screenshot driver screenshot-path)

                 ;; Navigate back
                 _ (e/back driver)
                 _ (e/wait driver 1)]
             {:title title
              :url url
              :content (take 500 content)  ; First 500 chars
              :screenshot screenshot-path})))))

Testing and Debugging
----------------------

**Use headless mode for CI/CD**

.. code-block:: clojure

   (defn test-login []
     (e/with-driver :firefox-headless driver
       (e/go driver "https://app.example.com/login")
       (e/fill-multi driver
                     :email "test@example.com"
                     :password "test123")
       (e/click driver :login-button)
       (e/wait-visible driver :dashboard)
       (assert (= "Dashboard" (e/get-title driver)))))

**Use headed mode for debugging**

.. code-block:: clojure

   (defn debug-automation []
     (e/with-driver :firefox driver
       ;; Visual feedback
       (e/go driver "https://example.com")
       (e/screenshot driver "before-action.png")
       (e/click driver :some-button)
       (e/wait driver 2)  ; Pause to observe
       (e/screenshot driver "after-action.png")))

**Postmortem debugging**

.. code-block:: clojure

   (e/with-postmortem driver {:dir "screenshots/errors"}
     ;; If any exception occurs in this block,
     ;; a screenshot will be automatically saved
     (e/go driver "https://example.com")
     (e/click driver :nonexistent-button))

Best Practices
--------------

1. **Always use explicit waits** instead of fixed sleeps when possible
2. **Clean up resources** with ``quit`` or use ``with-driver``
3. **Use headless mode** for production/CI to save resources
4. **Take screenshots** at key decision points for debugging
5. **Handle stale elements** by re-querying after page changes
6. **Set appropriate timeouts** based on your application
7. **Use try-catch** for error recovery in autonomous agents
8. **Check element state** before interaction (visible?, enabled?)
9. **Save cookies/session** to avoid repeated logins
10. **Use descriptive element queries** for maintainability

Troubleshooting
---------------

**Element not found**

- Ensure element is loaded with ``wait-exists`` or ``wait-visible``
- Check if element is in a frame - switch frames if needed
- Verify query selector is correct
- Check if page finished loading

**Stale element reference**

- Re-query the element after page changes
- Use wait predicates to ensure stability

**Timeout errors**

- Increase timeout with ``with-wait-timeout``
- Add explicit waits for dynamic content
- Check network speed and page load time

**Click not working**

- Ensure element is visible with ``wait-visible``
- Check if element is enabled with ``enabled?``
- Try scrolling to element first with ``scroll-query``
- Check if element is obscured by another element

Quick Reference Card
--------------------

**Setup**

.. code-block:: clojure

   (require '[etaoin.api :as e])
   (def driver (e/firefox))           ; Launch Firefox
   (e/with-driver :firefox driver ...) ; Auto-cleanup

**Navigate**

.. code-block:: clojure

   (e/go driver url)                  ; Navigate to URL
   (e/refresh driver)                 ; Reload page
   (e/back driver)                    ; Go back
   (e/forward driver)                 ; Go forward

**Query Elements**

.. code-block:: clojure

   (e/query driver :element-id)       ; By ID
   (e/query driver {:css "selector"}) ; By CSS
   (e/query driver {:tag :input :name "email"}) ; By attributes
   (e/query-all driver query)         ; All matching elements

**Interact**

.. code-block:: clojure

   (e/click driver query)             ; Click element
   (e/fill driver query "text")       ; Fill input
   (e/fill-multi driver [q1 "text1" q2 "text2"]) ; Multiple inputs
   (e/clear driver query)             ; Clear input
   (e/select driver query "option")   ; Select dropdown

**Extract Data**

.. code-block:: clojure

   (e/get-element-text driver query)  ; Get text content
   (e/get-element-attr driver query "attr") ; Get attribute
   (e/get-element-value driver query) ; Get input value
   (e/get-source driver)              ; Get page HTML
   (e/get-title driver)               ; Get page title
   (e/get-url driver)                 ; Get current URL

**Wait**

.. code-block:: clojure

   (e/wait-visible driver query)      ; Wait until visible
   (e/wait-exists driver query)       ; Wait until exists
   (e/wait-has-text driver query "text") ; Wait for text
   (e/wait driver seconds)            ; Simple delay

**Check State**

.. code-block:: clojure

   (e/exists? driver query)           ; Element exists?
   (e/visible? driver query)          ; Element visible?
   (e/enabled? driver query)          ; Element enabled?
   (e/selected? driver query)         ; Element selected?

**JavaScript**

.. code-block:: clojure

   (e/js-execute driver "return document.title;")
   (e/js-execute driver "return arguments[0] + 1;" 5)

**Capture**

.. code-block:: clojure

   (e/screenshot driver "path.png")   ; Screenshot page
   (e/get-cookies driver)             ; Get all cookies

**Cleanup**

.. code-block:: clojure

   (e/quit driver)                    ; Close browser

References
----------

- Etaoin GitHub: https://github.com/clj-commons/etaoin
- WebDriver W3C Spec: https://www.w3.org/TR/webdriver2/
- Etaoin User Guide: https://github.com/clj-commons/etaoin/blob/master/doc/01-user-guide.adoc
