Clojure experiments
===================

[Mejsla](http://www.mejsla.se/) and [Lars Westergren](http://twitter.com/#!/wgren) organised a very nice programming language unconference in Stockholm on November 29, 2011. One of the sessions was a hands-on Clojure lab, given by [Ville Sv&auml;rd](http://twitter.com/#!/villesv). Below is a [turtle graphics](http://en.wikipedia.org/wiki/Turtle_graphics) experiment I did during that lab.

I started by downloading [Clojure](http://clojure.org/getting_started), and I was surprised at how easy it was to get started. As an "old" Lisp programmer I felt at home! :)

Here goes the code for the Turtle Graphics experiment (shameless use of globals included):

    (def window (new javax.swing.JFrame))
    (.show window)
    (.setTitle window "Turtle Graphics")
    (.setSize window 500 500)

    (def canvas (.getGraphics window))
    (def black (new java.awt.Color 0 0 0))
    (def white (new java.awt.Color 255 255 255))

    (def orientation 0)

    (defn turn [angle]
      (def orientation (+ orientation angle)))

    (defn heading [angle]
      (def orientation angle))

    (defn moveto [x y]
      (def turtlex x)
      (def turtley y))

    (defn clear []
      (.setColor canvas white)
      (.fillRect canvas 0 0 5000 5000)
      (moveto 200 200))

    (defn fd [distance]
      (let [
        x1 turtlex
        y1 turtley
        x2 (+ turtlex 
             (* distance 
               (Math/cos
                 (Math/toRadians orientation))))
        y2 (+ turtley 
             (* distance 
               (Math/sin
                 (Math/toRadians orientation))))]
        (.setColor canvas black)
        (.drawLine canvas x1 y1 x2 y2)
        (def turtlex x2)
        (def turtley y2)))

	; Made my own version of dotimes ;)
    (defn dotimes [n fun]
      (when (> n 0) 
        (fun)
        (recur (- n 1) fun)))
      
    (defn square []
      (dotimes 4 
        (fn []
          (fd 100)
          (turn 90))))

    ; Clojure is a one pass compiler, so you need to declare
	; functions used in a double recursive way in advance.
    (declare dragon2)

    (defn dragon1 [size level]
       (if (= level 0) 
         (fd size)
         (do
           (dragon2 (* size 0.707) (- level 1))
           (turn -90)
           (dragon1 (* size 0.707) (- level 1)))))

    (defn dragon2 [size level]
       (if (= level 0) 
         (fd size)
		 ; do is like progn in Lisp
         (do
           (dragon2 (* size 0.707) (- level 1))
           (turn 90)
           (dragon1 (* size 0.707) (- level 1)))))
    
Test snippets:
	
    (dotimes 4
      (fn []
        (square)
        (turn 90)))
		
    (dotimes 360
      (fn []
        (square)
        (turn 1)))
	
    (dragon1 20 5)
