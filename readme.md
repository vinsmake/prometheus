probe_success{job="website_monitoring"}

http://localhost:9090/graph?g0.expr=probe_success&g0.tab=1&g0.display_mode=lines&g0.show_exemplars=0&g0.range_input=1h

http://localhost:9115/

http://localhost:9090/targets?search=

run .\prometheus.exe
run .\blackbox_exporter.exe --config.file=blackbox.yml#   p r o m e t h e u s - g r a p h a n a  
 