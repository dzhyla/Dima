
    //utils.addLogRecord("Result = "+cmd_result,0)
    var resp = JSON.parse(cmd_result);
    if (resp.status == 'OK'){
        ctx.instance.setValueToField('system_status', 3);
        deployResponce += "\n" + today + " - " + resp.message;
        ctx.instance.setValueToField('deploy_responce', deployResponce);
        var deplId = resp.message.substr(resp.message.length-11);
        ctx.instance.setValueToField('deployment_request_id', deplId);
        var idOfTicket = deplId.split('_');
        var ticketUrl = 'http://srv-002.nasctech.com/co/deploy_request/' + parseInt(idOfTicket[1]) + '/edit'
        ctx.instance.setValueToField('ticket_url', ticketUrl);
    } else if (resp.status == 'NOK'){
        ctx.instance.setValueToField('system_status', 4);
        deployResponce += "\n" + today + " - " + resp.message;
        ctx.instance.setValueToField('deploy_responce', deployResponce);
        ctx.instance.setValueToField('status', 2);
        ctx.instance.setValueToField('last_error', resp.message);
    }
    ctx.instance.setValueToField('last_updated_at',new Date());
    ctx.instance.save();
}

