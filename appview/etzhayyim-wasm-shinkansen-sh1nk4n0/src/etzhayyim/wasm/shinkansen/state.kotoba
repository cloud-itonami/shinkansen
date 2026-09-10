(ns etzhayyim.wasm.shinkansen.state
  "UI state for the shinkansen appview surface (static app metadata card).")

(def app
  {:title "Shinkansen Sh1nk4n0"
   :project "etzhayyim-project-shinkansen"
   :name "etzhayyim-wasm-shinkansen-sh1nk4n0"
   :kind "appview"
   :route-count 0
   :routes []
   :vars []
   :xrpc true
   :relative-path "60-apps/etzhayyim-project-shinkansen/appview/etzhayyim-wasm-shinkansen-sh1nk4n0"})

(defonce ^:private db (atom {:view :app}))

(defn current-view [] @db)

(defn set-view! [v]
  (swap! db assoc :view v))
